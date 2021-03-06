# Terra-Viva-API-definition
## Version : 1.0.0
api specification milestone

## Users related API
### /users/{username}
[GET] get user information
```sql
SELECT uuid, username, birth_date, subscribe_datetime, email, name, surname, verified, bio, profile_image
FROM
	users ;
```
[PUT] update user information
```sql
UPDATE users 
SET
	username = ?,
	birth_date = ?,
	email = ?,
	name = ?,
	surname = ?,
	verified = ?,
	bio = ?,
	profile_image = ?
WHERE uuid = ? ;
```
[DELETE] delete user account
```sql
DELETE FROM users
WHERE users.uuid=?;
```
[POST] add new user
```sql
INSERT INTO users
	( username, birth_date, subscribe_datetime, email, name, surname, verified, bio, profile_image) 
	VALUES ( ?, ?, ?, ?, ?, ?, ?, ?, ? );
```
### /users/{username}/snapshot
[GET] get profile image thumbnail and username
```sql
SELECT username, profile_image
FROM
	users ;
```
# User's varieties related API
### /users/{username}/my-varieties
[GET] get user varieties paged list
```sql
SELECT user_uuid, variety_id
FROM
	rt__user_varieties ;
```
### /users/{username}/my-varieties/{variety_id}
[DELETE] delete user variety
```sql
DELETE FROM rt__user_varieties
WHERE rt__user_varieties.user_uuid=? 
AND rt__user_varieties.variety_id=?;
```
### /users/{username}/my-varieties/followed

[GET] get the followed varieties paged list
### /users/{username}/my-varieties/followed/{variety_id}
```sql
SELECT  rt__users_varieties.variety_id, varieties.name
FROM rt__users_varieties LEFT JOIN varieties ON variety_id = id
WHERE user_uuid = ? ORDER BY varieties.name LIMIT ?, ?;
```
[POST] follow a variety
```sql
INSERT INTO variety_followers ( variety_id, user_uuid) 
VALUES ( ?, ? );
```
[DELETE] unfollow a variety
```sql
DELETE FROM variety_followers
WHERE user_uuid = ? AND variety_id = ?;
```
## User's tags realated API
### /users/{username}/my-tags/followed

[GET] get the paged list of the tags followed by the user
### /users/{username}/my-tags/followed/{tag_name}
```sql
SELECT  tags.uuid, tags.tag
FROM tag_followers LEFT JOIN tags ON tag_uuid = tags.uuid
WHERE user_uuid = ? ORDER BY tags.tag LIMIT ?, ?;
```
//sasi
[POST] follow the tag
```sql
INSERT INTO tag_followers
	( tag_uuid, user_uuid) VALUES ( ?, ? );
```
[DELETE] unfollow the tag
```sql
DELETE FROM tag_follwers
LEFT JOIN tags  ON tag_uuid = tags.uuid
WHERE user_uuid = ?  AND tag = ?;
```
## User's followe[r|d] realated API
**think to a better name for accounts**
### /users/{username}/accounts/followed

[GET] get the followed accounts paged list by the user
### /users/{username}/accounts/followed/{username}
```sql
SELECT user_followers.followed_uuid, users.username
LEFT JOIN users ON follower_uuid = users.uuid
WHERE  user_followers.follower_uuid = ? ORDER BY username;
```

[POST] follow a user
```sql
INSERT INTO user_followers (follower_uuid, followed_uuid,followe_datetime)
VALUES (?, ?, TIMESTAMP);
```

[DELETE] unfollow a user
```sql
DELETE FROM user_followers WHERE follower_uuid=? AND followed_uuid=?;
```
### /users/{username}/accounts/follower
[GET] get the followers paged list of the user
```sql
SELECT follower_uuid
FROM user_followers
WHERE followed_uuid = ?;
```

## User's species related API
### /users/{username}/my-species/followed
[GET] get the followed species list, paged and ordered by last followed first
```sql
SELECT id, scientific_name
FROM species_followers
	INNER JOIN species
	ON species_followers.species_id = species.id
WHERE species_followers.user_uuid = '';
```
### /users/{username}/my-species/followed/{species_id}
[POST] follow a species
```sql
INSERT INTO species_followers (user_uuid, species_id) VALUES (?, ?, TIMESTAMP);
```
[DELETE] unfollow a species
```sql
DELETE FROM species_followers
WHERE species_followers.user_uuid = ? AND species_followers.species_id = ?;
```

## User's post related API
### /users/{username}/posts
[GET] get the posts list of the user, paged and ordered by last created first
```sql
SELECT id, scientific_name
FROM species_followers
	INNER JOIN species
	ON species_followers.species_id = species.id
WHERE species_followers.user_uuid = ''
ORDER BY species_followers.follow_datetime DESC;
```
[POST] create a new post
```sql
INSERT INTO posts ( user_uuid, message) 
           VALUES ( ?, ? );
```

[PUT] modify a post
```sql
UPDATE posts 
SET 
    message = ? ;
WHERE posts.uuid = ? ;
```

### /users/{username}/posts/{post_uuid}
[DELETE] delete a post
```sql

```

## User's followed things
### /user/{username}/news
//TODO insieme\
[GET] list (paged and ordered by last created first) of:
- posts of a tag you follow
- new followers of users you follow
- new posts made by users you follow
- new media uploaded by users you follow
- new varieties of species you follow
- followers of varieties or species you follow

## Tags related API
### /tags
[POST] create a new tag
```sql
INSERT INTO tags ( tag) 
          VALUES ( ? );
```

### /tags/{tag_name}
[GET] get information about a tag
- how many times is used in posts
- how many follower has the tag
```sql
SELECT
  tag,
  COUNT(rt__posts_tags.tag_uuid) AS post_per_tag,
  COUNT(tag_followers.tag_uuid) AS followers_per_tag
FROM tags,rt__posts_tags, tag_followers
WHERE tags.tag = ''
  AND rt__posts_tags.tag_uuid = tags.uuid
  AND tag_followers.tag_uuid = tags.uuid;
```
### /tags/{tag_name}/posts
[GET] get the posts with the tag, paged and ordered by last created first
```sql
SELECT posts.uuid, message, created_datetime, users.username, users.profile_image
FROM tags
	INNER JOIN rt__posts_tags
	ON tags.uuid = rt__posts_tags.tag_uuid
	INNER JOIN posts
	ON posts.uuid = rt__posts_tags.posts_uuid
	INNER JOIN users
	ON posts.user_uuid = users.uuid 
WHERE tags.tag = '';
```
## Posts related 
### /posts/{post_uuid}
[GET] get the information about the post
```sql
SELECT user_uuid, uuid, message, created_datetime
FROM posts;
WHERE posts.uuid = ? ;
```

### /posts/{post_uuid}/comments
[GET] get the comments three of the posts
```sql
WITH RECURSIVE comments_tree (uuid, message, user_uuid, created_datetime, path) AS
(
  SELECT uuid, message, user_uuid, created_datetime, CAST(IFNULL(parent, post_uuid) AS VARCHAR(500)) as path
    FROM comments
    WHERE parent IS NULL AND post_uuid = '9fb2a1ed-fc46-11ec-9f76-02001700871b'
  UNION ALL
  SELECT c.uuid, c.message, c.user_uuid, c.created_datetime, CONCAT(ct.path, ' -> ',c.parent)
    FROM comments_tree AS ct JOIN comments AS c
      ON ct.uuid = c.parent
)
SELECT * FROM comments_tree
ORDER BY path;
```

## Like related
### /likes
[POST] the user like the post
```sql
INSERT INTO post_likes ( post_uuid, user_uuid) 
                VALUES ( ?, ? );
```

request:
body{
  resource_to_like: [post|comment]
  resource_uuid: uuid
  username: username
}

response:
body{
status_code: [201|204|404]
message: "response message"
}
