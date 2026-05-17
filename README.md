# Social Book 📖
 
A small-scale social media platform built with **Django** and **SQLite**, developed as a Database Systems course project at Dawood University of Engineering and Technology.
 
**Authors:** Wania Arif · Areeba Azam · Hooria Tariq  
**Course:** CS-2104 Database System  
 
---
 
## Overview
 
Social Book simulates core social networking features while demonstrating practical database design and management. Every user action — posting, liking, following — maps directly to a database transaction, making it a hands-on example of applied database management in a Python web application.
 
---
 
## Features
 
- User registration and authentication (sign up, sign in, log out)
- Profile management — display picture, bio, and location
- Create and upload posts (images with captions)
- Follow / unfollow other users
- Like and unlike posts (one like per user per post)
- Personalized feed — only shows posts from followed users
- User search by username
- Account settings page
---
 
## Tech Stack
 
| Layer     | Technology         |
|-----------|--------------------|
| Backend   | Python 3, Django 3.2 |
| Database  | SQLite3            |
| ORM       | Django ORM         |
| Frontend  | HTML, CSS (UIkit, Tailwind), JavaScript |
| Icons     | Font Awesome, Ionicons |
 
---
 
## Database Design
 
The database consists of four core tables:
 
### `core_profile`
Stores user profile information.
 
| Column      | Type         | Description              |
|-------------|--------------|--------------------------|
| id          | INTEGER (PK) | Auto-increment identifier |
| id_user     | INTEGER      | Links to Django auth user |
| user_id     | INTEGER      | Foreign reference to user |
| bio         | TEXT         | User biography           |
| profileimg  | VARCHAR(100) | Profile picture path     |
| location    | VARCHAR(100) | User location            |
 
### `core_post`
Stores user-created posts.
 
| Column      | Type         | Description              |
|-------------|--------------|--------------------------|
| id          | CHAR(32) (PK)| UUID identifier          |
| user        | VARCHAR(100) | Username of post owner   |
| caption     | TEXT         | Post caption             |
| created_at  | DATETIME     | Timestamp of creation    |
| no_of_likes | INTEGER      | Like counter             |
| image       | VARCHAR(100) | Image file path          |
 
### `core_likepost`
Tracks which users liked which posts.
 
| Column   | Type          | Description                   |
|----------|---------------|-------------------------------|
| id       | INTEGER (PK)  | Auto-increment identifier     |
| post_id  | VARCHAR(500) (FK) | References `core_post.id` |
| username | VARCHAR(100)  | Username of the liker         |
 
### `core_followerscount`
Tracks follower–following relationships.
 
| Column   | Type         | Description              |
|----------|--------------|--------------------------|
| id       | INTEGER (PK) | Auto-increment identifier |
| follower | VARCHAR(100) | Username of the follower |
| user     | VARCHAR(100) | Username being followed  |
 
> **Note:** Only `core_post → core_likepost` is enforced via a foreign key. Other relationships are maintained through application logic.
 
---
 
## Business Rules
 
1. Each user must have a unique username and email.
2. Every post must be associated with a valid user profile.
3. A user cannot follow the same person more than once.
4. A user cannot like the same post more than once.
5. A user cannot follow themselves.
6. Users can only view posts from people they follow.
7. Deleting a like decrements `no_of_likes`; adding one increments it.
---
 
## Project Structure
 
```
social_book/
├── core/
│   ├── models.py         # Profile, Post, LikePost, FollowersCount
│   ├── views.py          # All view functions (index, upload, search, like, follow, etc.)
│   ├── admin.py          # Admin registrations
│   └── urls.py           # App-level URL routing
├── templates/
│   ├── index.html        # Main feed
│   ├── profile.html      # User profile page
│   ├── search.html       # Search results
│   ├── setting.html      # Account settings
│   ├── signin.html       # Login page
│   └── signup.html       # Registration page
├── static/
│   └── assets/           # CSS, JS, images
├── social_book/
│   ├── settings.py       # Django project settings
│   └── urls.py           # Project-level URL routing
└── db.sqlite3            # SQLite database file
```
 
---
 
## Setup & Installation
 
### Prerequisites
- Python 3.8+
- pip
### Steps
 
```bash
# 1. Clone the repository
git clone <repository-url>
cd social_book
 
# 2. Create and activate a virtual environment
python -m venv venv
source venv/bin/activate        # On Windows: venv\Scripts\activate
 
# 3. Install dependencies
pip install django pillow
 
# 4. Apply database migrations
python manage.py makemigrations
python manage.py migrate
 
# 5. Create a superuser (optional, for admin access)
python manage.py createsuperuser
 
# 6. Run the development server
python manage.py runserver
```
 
Then open your browser at `http://127.0.0.1:8000`.
 
---
 
## Sample SQL Queries
 
**Users with more than 3 followers:**
```sql
SELECT user
FROM core_followerscount
GROUP BY user
HAVING COUNT(*) > 3;
```
 
**Post summary with total likes:**
```sql
SELECT p.id, p.user, p.caption,
       (SELECT COUNT(*) FROM core_likepost l
        WHERE REPLACE(l.post_id, '-', '') = REPLACE(p.id, '-', '')) AS total_likes
FROM core_post p;
```
 
**Follower count per user:**
```sql
SELECT user, COUNT(follower) AS total_followers
FROM core_followerscount
GROUP BY user;
```
 
**Join likes with post details:**
```sql
SELECT *
FROM core_likepost l
JOIN core_post p
  ON REPLACE(p.id, '-', '') = REPLACE(l.post_id, '-', '');
```
 
---
 
## Database Triggers
 
Three triggers are defined directly in SQLite:
 
- **`update_post_likes_after_insert`** — increments `no_of_likes` on `core_post` when a new like is added.
- **`update_post_likes_after_delete`** — decrements `no_of_likes` when a like is removed.
- **`prevent_self_follow`** — raises an abort error (`SQLITE_CONSTRAINT_TRIGGER`) if a user attempts to follow themselves.
---
 
## Known Limitations
 
- SQLite does not enforce all foreign key constraints by default; referential integrity is maintained through application logic.
- The notification and messaging UI elements in the templates are static placeholders and not yet wired to backend logic.
- No pagination is implemented on the main feed.
- The application is configured for local development only (`DEBUG = True`); it is not production-ready as-is.
---
 
## Future Improvements
 
- Migrate to PostgreSQL or MySQL for better scalability.
- Add comments, notifications, and direct messaging features.
- Implement REST APIs for potential mobile app integration.
- Add role-based access control and stronger input validation.
- Improve frontend responsiveness and UX.
---
 
## References
 
- freeCodeCamp.org — [Build a Social Media App with Django](https://youtu.be/xSUm6iMtREA)
- freeCodeCamp.org — [Django CRM App Tutorial](https://youtu.be/t10QcFx7d5k)
- freeCodeCamp.org — [SQLite Databases With Python - Full Course](https://youtu.be/byHcYRpMgI4)
---
 
## License
 
This project was developed for academic purposes as part of the CS-2104 Database Systems course.
