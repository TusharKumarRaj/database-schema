# Thrift Store Backend – Database Design

## Overview

This project models an Instagram-style thrift marketplace where each product listing represents a single unique item. The system supports product listing, media management, order processing, payments, bookmarking, reviews, and user-to-user messaging.

The schema is designed to ensure:

* Each product can be sold only once
* Clear separation between different system concerns
* Support for both transactional and social features

---

## Users

The `users` table manages user identity, authentication, and roles.

```sql
users {
  id serial primary key

  username varchar(50) unique not null
  email varchar(100) unique not null
  mobile char(15) unique
  dob date
  avatar_url text null
  password varchar(256) not null

  role char(6) check (role in ('buyer','seller')) default 'buyer'

  forgot_password_token text
  email_verification_token text

  created_at timestamp
  updated_at timestamp
}
```

---

## Product Posts

The `product_posts` table represents items listed by sellers.

```sql
product_posts {
  id serial primary key

  caption varchar(256) not null
  description text not null
  price numeric(10,2) not null 

  status varchar(20) check (status in ('draft','available','reserved','sold','expired')) default 'draft'
  
  size varchar(10) null
  condition varchar(20)
  brand varchar(50)

  author_id int foreign key

  created_at timestamp
  updated_at timestamp 
}
```

---

## Product Images

Stores images associated with product posts.

```sql
product_images {
  id serial primary key
  image_url text not null

  post_id int foreign key

  created_at timestamp
  updated_at timestamp 
}
```

---

## Orders

Represents purchase transactions initiated by buyers.

```sql
orders {
  id serial primary key

  paid_amount numeric(10,2) not null

  buyer_id int foreign key

  status varchar(20) check (status in ('placed','confirmed','shipped','delivered','cancelled')) default 'placed'

  created_at timestamp
  updated_at timestamp 
}
```

---

## Order Items

Links orders to product posts and enforces single-item transactions.

```sql
order_items {
  id serial primary key

  purchase_price numeric(10,2) not null

  order_id int foreign key unique
  post_id int foreign key unique

  created_at timestamp
  updated_at timestamp 
}
```

---

## Stores

Represents seller storefronts.

```sql
stores {
  id serial primary key

  user_id int foreign key
  store_name varchar(100) unique not null

  created_at timestamp
  updated_at timestamp
}
```

---

## Payments

Handles payment records for orders.

```sql
payments {
  id serial primary key
  order_id int foreign key
  amount numeric(10,2)
  payment_method varchar(20)
  status varchar(20) check (status in ('pending','success','failed')) default 'pending'

  transaction_id varchar(100) unique

  created_at timestamp
  updated_at timestamp
}
```

---

## Bookmarks

Allows users to save products.

```sql
bookmarks {
  id serial primary key

  user_id int not null
  post_id int not null

  created_at timestamp

  unique(user_id, post_id)
}
```

---

## Reviews

Allows buyers to review sellers after a transaction.

```sql
reviews {
  id serial primary key

  reviewer_id int not null
  seller_id int not null

  order_id int unique

  rating varchar check (rating in ('1','2','3','4','5'))
  comment text

  created_at timestamp
  updated_at timestamp
}
```

---

## Conversations

Represents a chat between two users.

```sql
conversations {
  id serial primary key

  user1_id int not null
  user2_id int not null

  created_at timestamp
}
```

---

## Messages

Stores messages within a conversation.

```sql
messages {
  id serial primary key

  conversation_id int not null
  sender_id int not null

  message text
  image_url text null

  created_at timestamp
}
```

---

## Relationships

```sql
users.id < product_posts.author_id
product_posts.id < product_images.post_id

users.id < orders.buyer_id
orders.id < order_items.order_id
product_posts.id - order_items.post_id

users.id < stores.user_id

orders.id - payments.order_id

users.id < bookmarks.user_id
product_posts.id < bookmarks.post_id

users.id < reviews.reviewer_id
users.id < reviews.seller_id
orders.id < reviews.order_id

users.id < conversations.user1_id
users.id < conversations.user2_id

conversations.id < messages.conversation_id
users.id < messages.sender_id
```

---

## Key Design Decisions

### Single Product per Order

Each order contains exactly one product. This is enforced using:

* `unique(order_id)` in `order_items`
* `unique(post_id)` in `order_items`

### Product Lifecycle Management

Product availability is handled using the `status` field:

* `draft`
* `available`
* `reserved`
* `sold`
* `expired`

### Separation of Media

Product images are stored in a separate table to allow multiple images per product.

### Decoupled Payments

Payments are stored independently from orders to support flexibility in handling transactions.

### Bookmark System

A many-to-many relationship between users and products allows users to save items.

### Review System

Reviews are tied to orders to ensure that only actual buyers can review sellers.

### Messaging System

The messaging system is implemented using:

* `conversations` to group users
* `messages` to store communication

---

## Constraints and Integrity

* Unique constraints ensure no duplicate bookmarks, reviews, or product sales
* Check constraints enforce valid states for roles, statuses, and ratings
* Foreign keys maintain referential integrity across tables

---

## Extensibility

The schema is designed to support future enhancements such as:

* Notifications
* Real-time messaging
* Seller analytics
* Recommendation systems
* Advanced payment integrations

---

## Conclusion

This database design provides a structured and scalable foundation for a thrift marketplace. It balances simplicity with real-world requirements, ensuring data integrity while supporting both transactional and social interactions.
