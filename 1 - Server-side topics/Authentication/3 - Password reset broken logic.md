## Password reset broken logic

This lab's password reset functionality is vulnerable. To solve the lab, reset Carlos's password then log in and access his "My account" page.

Your credentials: wiener:peter
Victim's username: carlos

## Notes

- Possibility to leave a comment on a post
- Exploit server
- /login
- Forgot password feature

## Resume

1. Reset wiener's password
2. Use the reset link in mail
3. Intercept the **POST** request updating the password
4. Edit request with `username=carlos`
5. Login with Carlos

## Solve 

The first step is to reset wiener account, and check every request from the forgot password feature. On the last request before updating the password, there is a hidden parameter with the username : 

`temp-forgot-password-token=65z5h5t76g65maajbrowibi38rd6njvm&username=wiener&new-password-1=peter&new-password-2=peter`

We can repeat all the steps and intercept the **POST** password reset request. We need to update the user from `wiener` to `carlos` and **forward** the request : 

`temp-forgot-password-token=65z5h5t76g65maajbrowibi38rd6njvm&username=carlos&new-password-1=peter&new-password-2=peter`

We can now log in with **Carlos** using the password we just updated. 