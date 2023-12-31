# E-Wallet Database Design

### Entity Relationship Diagram

![Alt text](erd.drawio.png?raw=true "Entity Relationship Diagram")

### Assumptions

1. One wallet per user
2. Password record that got saved in the database already saved in hashed
3. Reward from winning game does not considered as top up

## Data Definition Language

```sql
-- 1. Make new database wallet_db
CREATE DATABASE wallet_db;

-- 2. Make new users table
CREATE TABLE users (
  id BIGSERIAL PRIMARY KEY,
  user_name VARCHAR NOT NULL,
  birthdate DATE NOT NULL,
  email VARCHAR UNIQUE NOT NULL,
  user_password VARCHAR NOT NULL,
  phone VARCHAR UNIQUE NOT NULL,
  created_at TIMESTAMP NOT NULL,
  updated_at TIMESTAMP NOT NULL,
  deleted_at TIMESTAMP
);

-- 3. Make new wallets table
CREATE TABLE wallets (
  id BIGSERIAL PRIMARY KEY,
  user_id BIGINT NOT NULL,
  balance DECIMAL NOT NULL,
  created_at TIMESTAMP NOT NULL,
  updated_at TIMESTAMP NOT NULL,
  deleted_at TIMESTAMP,
  FOREIGN KEY (user_id) REFERENCES users(id)
);

-- 4. Make new source_funds table
CREATE TABLE source_funds (
  id BIGSERIAL PRIMARY KEY,
  fund_category VARCHAR NOT NULL,
  created_at TIMESTAMP NOT NULL,
  updated_at TIMESTAMP NOT NULL,
  deleted_at TIMESTAMP
);

-- 5. Make new game_chances table
CREATE TABLE game_chances (
  id BIGSERIAL PRIMARY KEY,
  user_id BIGINT NOT NULL,
  user_chance INTEGER NOT NULL,
  created_at TIMESTAMP NOT NULL,
  updated_at TIMESTAMP NOT NULL,
  deleted_at TIMESTAMP,
  FOREIGN KEY (user_id) REFERENCES users(id)
);

-- 6. Make new transaction_histories table
CREATE TABLE transaction_histories (
  id BIGSERIAL PRIMARY KEY,
  sender_id BIGINT,
  recipient_id BIGINT NOT NULL,
  description VARCHAR NOT NULL,
  amount DECIMAL NOT NULL,
  source_fund_id BIGINT NOT NULL,
  transaction_time TIMESTAMP NOT NULL,
  created_at TIMESTAMP NOT NULL,
  updated_at TIMESTAMP NOT NULL,
  deleted_at TIMESTAMP,
  FOREIGN KEY (sender_id) REFERENCES users(id),
  FOREIGN KEY (recipient_id) REFERENCES users(id),
  FOREIGN KEY (source_fund_id) REFERENCES source_funds(id)
);

-- 7. Make new token_passwords table
CREATE TABLE token_passwords (
  id BIGSERIAL PRIMARY KEY,
  user_id BIGINT NOT NULL,
  recover_token VARCHAR NOT NULL,
  is_used BOOLEAN NOT NULL,
  is_expired BOOLEAN NOT NULL,
  expiration_time TIMESTAMP NOT NULL,
  created_at TIMESTAMP NOT NULL,
  updated_at TIMESTAMP NOT NULL,
  deleted_at TIMESTAMP,
  FOREIGN KEY (user_id) REFERENCES users(id)
);
```

## Data Manipulation Language

```sql
-- 1. Seeding users table with user data
INSERT INTO users(user_name, birthdate, email, user_password, phone, created_at, updated_at)
VALUES
('Adit', '2001-06-14', 'adit@abc.com', 'N0q0kt65O4uhY4c', '081290882428', NOW(), NOW()),
('Budi', '2001-11-30', 'budi@xyz.com', 'FNX40HB6ZT4rrrF', '081290882421', NOW(), NOW()),
('Chika', '1997-02-28', 'chika@abc.com', '2DjFJwwUMySFQIm', '081290882422', NOW(), NOW()),
('Dira', '1990-05-29', 'dira@abc.com', 'wL0HqZ47Ujj4jRP', '081290882423', NOW(), NOW()),
('Evan', '2003-12-15', 'evan@xyz.com', 'gUe2Cwb4yIyzgum', '081290882424', NOW(), NOW())
;

-- 2. Seeding source_funds table with fund category that is supported
INSERT INTO source_funds(fund_category, created_at, updated_at)
VALUES
('Bank Transfer', NOW(), NOW()),
('Merchant Partner', NOW(), NOW()),
('Wallet Transfer', NOW(), NOW()),
('Gacha Wins', NOW(), NOW())
;

-- 3. Seeding wallets table with wallets balance data for each user
INSERT INTO wallets(user_id, balance, created_at, updated_at)
VALUES
(1, 3568000, NOW(), NOW()),
(2, 1460000, NOW(), NOW()),
(3, 2086000, NOW(), NOW()),
(4, 1194000, NOW(), NOW()),
(5, 1655000, NOW(), NOW())
;

-- 4. Seeding game_chances table with user_chance data
INSERT INTO game_chances(user_id, user_chance, created_at, updated_at)
VALUES
(1, 6, NOW(), NOW()),
(2, 4, NOW(), NOW()),
(3, 3, NOW(), NOW()),
(4, 1, NOW(), NOW()),
(5, 2, NOW(), NOW())
;

-- 5. Seeding transaction_histories table with transaction data
INSERT INTO transaction_histories(sender_id, recipient_id, description, amount, source_fund_id, transaction_time, created_at, updated_at)
VALUES
(1, 1, 'Bank Top Up', 2500000, 1, '2021-12-03 20:31:09', '2021-12-03 20:31:09', '2021-12-03 20:31:09'),
(2, 2, 'Bank Top Up', 1000000, 1, '2021-12-20 20:34:22', '2021-12-20 20:34:22', '2021-12-20 20:34:22'),
(3, 3, 'Bank Top Up', 1500000, 1, '2022-02-13 09:10:21', '2022-02-13 09:10:21', '2022-02-13 09:10:21'),
(4, 4, 'Bank Top Up', 500000, 1, '2022-04-19 12:28:41', '2022-04-19 12:28:41', '2022-04-19 12:28:41'),
(5, 5, 'Bank Top Up', 500000, 1, '2022-06-08 00:48:21', '2022-06-08 00:48:21', '2022-06-08 00:48:21'),
(1, 1, 'Bank Top Up', 270000, 1, '2022-06-08 18:22:11', '2022-06-08 18:22:11', '2022-06-08 18:22:11'),
(2, 2, 'Bank Top Up', 280000, 1, '2022-06-09 06:09:33', '2022-06-09 06:09:33', '2022-06-09 06:09:33'),
(3, 3, 'Bank Top Up', 290000, 1, '2022-06-09 14:21:01', '2022-06-09 14:21:01', '2022-06-09 14:21:01'),
(4, 4, 'Bank Top Up', 210000, 1, '2022-06-10 00:35:34', '2022-06-10 00:35:34', '2022-06-10 00:35:34'),
(5, 5, 'Bank Top Up', 370000, 1, '2022-06-12 16:11:43', '2022-06-12 16:11:43', '2022-06-12 16:11:43'),
(1, 5, 'Wallet Transfer', 100000, 3, '2022-06-13 10:32:01', '2022-06-13 10:32:01', '2022-06-13 10:32:01'),
(1, 3, 'Wallet Transfer', 50000, 3, '2022-06-13 19:02:19', '2022-06-13 19:02:19', '2022-06-13 19:02:19'),
(2, 2, 'Merchant Top Up', 75000, 2, '2022-06-15 05:19:37', '2022-06-15 05:19:37', '2022-06-15 05:19:37'),
(3, 3, 'Merchant Top Up', 37000, 2, '2022-06-15 19:49:20', '2022-06-15 19:49:20', '2022-06-15 19:49:20'),
(5, 5, 'Merchant Top Up', 235000, 2, '2022-06-16 14:09:30', '2022-06-16 14:09:30', '2022-06-16 14:09:30'),
(5, 2, 'Wallet Transfer', 50000, 3, '2022-06-21 09:13:00', '2022-06-21 09:13:00', '2022-06-21 09:13:00'),
(5, 4, 'Wallet Transfer', 50000, 3, '2022-06-25 15:51:01', '2022-06-25 15:51:01', '2022-06-25 15:51:01'),
(5, 3, 'Wallet Transfer', 50000, 3, '2022-06-26 12:48:24', '2022-06-26 12:48:24', '2022-06-26 12:48:24'),
(1, 1, 'Bank Top Up', 300000, 1, '2022-06-27 04:43:44', '2022-06-27 04:43:44', '2022-06-27 04:43:44'),
(4, 4, 'Bank Top Up', 200000, 1, '2022-06-29 17:45:33', '2022-06-29 17:45:33', '2022-06-29 17:45:33'),
(1, 1, 'Gacha Rewards', 200000, 4, '2022-09-13 20:56:37', '2022-09-13 20:56:37', '2022-09-13 20:56:37'),
(1, 5, 'Wallet Transfer', 200000, 3, '2022-10-23 05:06:15', '2022-10-23 05:06:15', '2022-10-23 05:06:15'),
(4, 4, 'Gacha Rewards', 42000, 4, '2022-10-28 21:48:05', '2022-10-28 21:48:05', '2022-10-28 21:48:05'),
(3, 5, 'Wallet Transfer', 50000, 3, '2022-11-13 06:36:31', '2022-11-13 06:36:31', '2022-11-13 06:36:31'),
(2, 2, 'Merchant Top Up', 200000, 2, '2022-11-23 04:45:02', '2022-11-23 04:45:02', '2022-11-23 04:45:02'),
(2, 3, 'Wallet Transfer', 200000, 3, '2022-12-31 03:43:32', '2022-12-31 03:43:32', '2022-12-31 03:43:32'),
(4, 4, 'Merchant Top Up', 500000, 2, '2023-01-27 08:12:34', '2023-01-27 08:12:34', '2023-01-27 08:12:34'),
(4, 5, 'Wallet Transfer', 350000, 3, '2023-02-16 00:12:47', '2023-02-16 00:12:47', '2023-02-16 00:12:47'),
(1, 1, 'Bank Top Up', 650000, 1, '2023-04-04 16:32:20', '2023-04-04 16:32:20', '2023-04-04 16:32:20'),
(1, 1, 'Gacha Rewards', 8000, 4, '2023-05-09 23:13:18', '2023-05-09 23:13:18', '2023-05-09 23:13:18'),
(2, 2, 'Gacha Rewards', 65000, 4, '2023-06-25 09:12:51', '2023-06-25 09:12:51', '2023-06-25 09:12:51'),
(3, 3, 'Gacha Rewards', 9000, 4, '2023-07-31 04:20:37', '2023-07-31 04:20:37', '2023-07-31 04:20:37'),
(4, 4, 'Gacha Rewards', 42000, 4, '2023-08-20 20:41:37', '2023-08-20 20:41:37', '2023-08-20 20:41:37'),
(5, 5, 'Gacha Rewards', 0, 4, '2023-08-26 17:56:57', '2023-08-26 17:56:57', '2023-08-26 17:56:57'),
(2, 1, 'Wallet Transfer', 10000, 3, '2023-09-17 04:53:02', '2023-09-17 04:53:02', '2023-09-17 04:53:02')
;

-- 6. Seeding token_passwords table with attempt to recover passwords
INSERT INTO token_passwords(user_id, recover_token, is_used, is_expired, expiration_time, created_at, updated_at, deleted_at)
VALUES
(1, 'oQfCbV', false, true, '2022-01-17 22:43:59', '2022-01-17 21:43:59', '2022-01-17 22:43:59', '2022-01-17 22:43:59'),
(1, 'GBUxX6', true, false, '2022-01-17 23:44:00', '2022-01-17 22:44:00', '2022-01-17 23:45:37', '2022-01-17 23:45:37'),
(5, 'EQ1Ra3', true, false, '2022-10-06 14:08:31', '2022-10-06 13:08:31', '2022-10-06 13:09:31', '2022-10-06 13:09:31')
;
```

## Data Query Language

```sql
-- 1. Get all of the transaction history data from this year. Order the data from the oldest to the latest
SELECT 
	*
FROM
	transaction_histories th
WHERE EXTRACT(YEAR FROM transaction_time) = '2023'
ORDER BY transaction_time ASC;

-- 2. Get the amount of top up transactions data on June last year, group them by source of funds. Order the data by the smallest amount of money to the largest
SELECT 
	description AS source_of_funds,
	SUM(amount) AS sum_amount
FROM
	transaction_histories th
WHERE 
	EXTRACT(YEAR FROM transaction_time) = '2022' AND
	EXTRACT(MONTH FROM transaction_time) = '6' AND
	RIGHT(description, 6) = 'Top Up'
GROUP BY description
ORDER BY sum_amount ASC;

-- 3. Get all the users data, along with the total money in their wallet. Order the data by the largest amount of money to the smallest
SELECT
	u.id,
	u.user_name,
	u.birthdate,
	u.email,
	u.user_password,
	u.phone,
	w.balance
FROM
	users u
	LEFT JOIN wallets w ON u.id = w.user_id
ORDER BY w.balance DESC;
```

## Database Transaction

1. `Top up and transfer transaction from the same account that happen at the same time` may cause a database transaction `lost update problems`, this transaction table below will illustrate how that could happen
```
Time|       Top Up Tx        |   x   |       Transfer Tx      |   y   | balance |
t1  |          ...           |   -   |         BEGIN          |   -   |  3000   |
t2  |         BEGIN          |   -   |          ...           |   -   |  3000   |
t3  |          ...           |   -   | READ balance, put to y | 3000  |  3000   |
t4  | READ balance, put to x | 3000  |	        ...           |	3000  |  3000   |
t5  |      x = x + 1000      | 4000  |	        ...           | 3000  |  3000   |
t6  |          ...           | 4000  |	     y = y - 500      | 2500  |  2500   |
t7  |  UPDATE balance = x    | 4000  |          ...           |	2500  |  2500   |
t8  |         COMMIT         |	 -   |          ...           | 2500  |  2500   |
t9  |          ...           |   -   |   UPDATE balance = y   |	2500  |  2500   |
t10 |          ...           |   -   |         COMMIT         |   -   |	 2500   |
```

from the table above we know that transaction 1 is doing a top up and transaction 2 is transfering, transaction 1 and 2 read the balance at the same time, then transaction 1 updates the data followed by transaction 2. At the end, instead of having 4000 as the final balance, the account will have 2500 as the final balance

- initial balance is 3000
- balance got increased by top-up of 1000
- then deducted by transfer of 500

`The update from top-up transaction is lost, hence lost update problems happen`
