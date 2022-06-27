# Terra-Viva-API-definition
## Version : 1.0.0
api specification milestone

## Users related API
### /users/{username}
[GET] get user information
[PUT] update user information
[DELETE] delete user account
[POST] add new user
### /users/{username}/snapshot
[GET] get profile image thumbnail and username

## User's varieties related API
### /users/{username}/my-varieties
[GET] get user varieties paged list
### /users/{username}/my-varieties/{variety_id}
[DELETE] delete user variety
### /users/{username}/my-varieties/followed
[GET] get the followed varieties paged list
### /users/{username}/my-varieties/followed/{variety_id}
[POST] follow a variety
[DELETE] unfollow a variety

## User's tags realated API
### /users/{username}/my-tags/followed
[GET] get the paged list of the tags followed by the user
### /users/{username}/my-tags/followed/{tag_name}
[POST] follow the tag
[DELETE] unfollow the tag

## User's followe[r|d] realated API
**think to a better name for accounts**
### /users/{username}/accounts/followed
[GET] get the followed accounts paged list by the user
### /users/{username}/accounts/followed/{username}
[POST] follow a user
[DELETE] unfollow a user
### /users/{username}/accounts/follower
[GET] get the followers paged list of the user

## User's species related API
### /users/{username}/my-species/followed
[GET] get the followed species list, paged and ordered by last followed first
### /users/{username}/my-species/followed/{species_id}
[POST] follow a species
[DELETE] unfollow a species

## User's post related API
### /users/{username}/posts
[GET] get the posts list of the user, paged and ordered by last created first
[POST] create a new post
[PUT] modify a post
### /users/{username}/posts/{post_uuid}
[DELETE] delete a post

## User's followed things
### /user/{username}/news
[GET] list (paged and ordered by last created first) of:
- posts of a tag you follow
- new followers of users you follow
- new posts made by users you follow
- new strains added by users you follow
- new media uploaded by users you follow
- new varieties of species you follow
- followers of varieties or species you follow

## Tags related API
### /tags
[POST] create a new tag
### /tags/{tag_name}
[GET] get information about a tag
### /tags/{tag_name}/posts
[GET] get the posts with the tag, paged and ordered by last created first

## Posts related 
### /posts/{post_uuid}
[GET] get the information about the post
### /posts/{post_uuid}/comments
[GET] get the comments three of the posts

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
