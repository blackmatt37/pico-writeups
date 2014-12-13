## Injection 2 (110)

The login.php file is clearly insecure since it inserts a user-supplied string without checking it for injections first.  Therefore, we can easily inject our own row into the query.

First, we need to remove any rows selected by the query, so the username we input starts with `' AND 1=0` (to end the string and to prevent anything from being selected).  Next, we have to create our fake row with `UNION SELECT [cols]`, and finally comment out the rest of the query with `-- --`.

Figuring out the cols is a matter of trial and error, but you can easily find that there are five columns and that the third of these is the password.  (This is easiest to do with the `debug` flag enabled.)  Replacing all of the other columns with `1337` gives us sufficient priviliges to access the flag.

Username: `' AND 1=0 UNION SELECT 'username', 1337, 'password', 1337, 1337-- --`

Password: `password`

Flag: `flag_nJZAKGWYt7YfzmTsCV`