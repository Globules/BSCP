## Modifying serialized data types

This lab uses a serialization-based session mechanism and is vulnerable to authentication bypass as a result. To solve the lab, edit the serialized object in the session cookie to access the administrator account. Then, delete the user carlos.

You can log in to your own account using the following credentials: wiener:peter

Hint :

To access another user's account, you will need to exploit a quirk in how PHP compares data of different types.

Note that PHP's comparison behavior differs between versions. This lab assumes behavior consistent with PHP 7.x and earlier.

## Notes



## Resume



## Solve