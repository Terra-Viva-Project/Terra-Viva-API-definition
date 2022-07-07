# Terra-Viva-API-definition
## Version : 1.0.0
api specification milestone

## Users related API
### /users/{username}
//sasi
[GET] get user information
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
//sasi
[DELETE] delete user account
//sasi
[POST] add new user
### /users/{username}/snapshot
//sasi
[GET] get profile image thumbnail and username

# User's varieties related API
### /users/{username}/my-varieties
//sasi
[GET] get user varieties paged list
### /users/{username}/my-varieties/{variety_id}
//sasi
[DELETE] delete user variety
### /users/{username}/my-varieties/followed
//nic
[GET] get the followed varieties paged list
### /users/{username}/my-varieties/followed/{variety_id}
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
//nic
[GET] get the paged list of the tags followed by the user
### /users/{username}/my-tags/followed/{tag_name}
//sasi
[POST] follow the tag
//nic
[DELETE] unfollow the tag

## User's followe[r|d] realated API
**think to a better name for accounts**
### /users/{username}/accounts/followed
//nic
[GET] get the followed accounts paged list by the user
### /users/{username}/accounts/followed/{username}
//nic
[POST] follow a user
//nic
[DELETE] unfollow a user
### /users/{username}/accounts/follower
[GET] get the followers paged list of the user

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
//nic
[POST] follow a species
[DELETE] unfollow a species

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
[PUT] modify a post
### /users/{username}/posts/{post_uuid}
[DELETE] delete a post

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
