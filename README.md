# Terra-Viva-API-definition
this repository contains the api definition

## Users related API
### /users/{username}
[GET] get user information
[PUT] update user information
[DELETE] delete user account
[POST] add new user
### /users/{username}/snapshot
[GET] get profile image thumbnail and username

## User's varieties related API
### /users/{username}/varieties
[GET] get user varieties list
### /users/{username}/varieties/{variety_id}
[DELETE] delete user variety
### /users/{username}/varieties/followed
[GET] get the followed varieties list
### /users/{username}/varieties/followed/{variety_id}
[DELETE] unfollowe a variety

## User's tags realated API
### /users/{username}/tags/followed
[GET] get the tags followed by the user
### /users/{username}/tags/followed/{tag_name}
[DELETE] unfollow the tag

## User's followe[r|d] realated API
### /users/{username}/acounts/followed
[GET] get the users followed by the user
### /users/{username}/accounts/followed/{username}
[DELETE] unfollow a user
### /users/{username}/accounts/follower
[GET] get the list of the follower of the user

## User's species related API
### /users/{username}/species/followed
[GET] get the followed species list
### /users/{username}/species/followed/{species_id}
[DELETE] unfollow a species

## User's post related API
### /users/{username}/posts
[GET] get the posts list of the user
[POST] create a new post
[PUT] modify a post
### /users/{username}/posts/{post_uuid}
[DELETE] delete a post



## Tags related API
### /tags
[POST] create a new tag
### /tags/{tag_name}
[GET] get information about a tag
### /tags/{tag_name}/posts
[GET] get the post with the {tag_name} tag
