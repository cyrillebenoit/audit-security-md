
# Vulnerability Audit


**Cyrille Benoit** & **Léo Dorbes**

## I- Command Injection

The ping command is executed directly on the server. This represents a vulnerability where users can browse files directly and cause damages to the system. For instance, we found this interesting file entering `; cat ../../CHANGELOG.md`.

![ci.png](/images/ci.png)

### Attack story

A hacker destroys all files needed for the website directly on the server.

### Risk Analysis

| Frequency | Effect |
|----------:|-------:|
|         2 |      3 |

### Recommendation

 - Validate user input through an applicative parse to check if it fits criterias.

### Risk Analysis 2

| Frequency | Effect |
|----------:|-------:|
|         0 |      0 |

---
## II- CSRF

The admin password can be changed from another website calling the route 
`http://192.168.10.100/vulnerabilities/csrf/?password_new=PASS&password_conf=PASS&Change=Change#` in the background (using javascript for instance)

This will rely on the current session used by the user and he won't notice anything before trying logging back again.

### Attack story

A hacker changes the password of the victim on a shopping website and uses his account.

### Risk Analysis

| Frequency | Effect |
|----------:|-------:|
|         1 |      2 |

### Recommendation

 - Generate a unique token for each form. No form without token should be validated by the website.

### Risk Analysis 2

| Frequency | Effect |
|----------:|-------:|
|         1 |      0 |

---
## III- File Inclusion

The files can be accessed directly if the user changes the parameters of the URL in his browser. This allows the user to access pages he shouldn't be able to.

![fi-ci.png](/images/fi-ci.png)

![fi.png](/images/fi-ci.png)

### Attack story

A user accede to a hidden page (admin panel) on the website.

### Risk Analysis

| Frequency | Effect |
|----------:|-------:|
|         3 |      2 |

### Recommendation

 - Verify user input

### Risk Analysis 2

| Frequency | Effect |
|----------:|-------:|
|         0 |      0 |

---
## IV- File Upload

There is no restriction on the type of the file the user can upload. This can represent a risk for the company who owns the website if users decide to send compromised files and/or have them executed by the server.

![fu]()

### Attack story

A group of malicious users distributes a virus in a fake PDF file on a forum.

### Risk Analysis

| Frequency | Effect |
|----------:|-------:|
|         2 |      2 |

### Recommendation

- Restrict the MIME type of the file
- Limit the file max size
- Upload the file in a sandbox
- Install an antivirus software on the server

### Risk Analysis 2

| Frequency | Effect |
|----------:|-------:|
|         2 |      0 |

---
## V- SQL Injection

The injection seems pretty limited there, the hacker cannot fetch the names of the tables used by the SQL user.

 - `1' || true;#` - This will return all elements from the table.
 - `1'; SHOW TABLES;#` - The result is not shown in the webpage, but a command like 
 
```mysql
1';
DROP DATABASE (SELECT DATABASE());#
```

or 
```mysql
1';
SET @name = (SELECT DATABASE());
SELECT @name;
DROP DATABASE @name;#
```

(better written) should be able to disrupt databases on the server. We've tried variations of that for hours without any success.

![sqli-all.png](/images/sqli-all.png)
![sqli-fail.png](/images/sqli-fail.png)

### Attack story

A company steals the user databases from its competitors.

### Risk Analysis

| Frequency | Effect |
|----------:|-------:|
|         4 |      3 |

### Recommendation

 - Use prepared statements
 - Escape SQL characters from user input

### Risk Analysis 2

| Frequency | Effect |
|----------:|-------:|
|         0 |      0 |

---
## VI- SQL Injection (Blind)

This is similar to SQL Injection but even less frequent since the user cannot get any return from the DBMS.

![sqli-blind.png](/images/sqli-blind.png)

### Attack story

A company destroys the user databases from its competitors.

### Risk Analysis

| Frequency | Effect |
|----------:|-------:|
|         3 |      3 |

### Recommendation

 - Use prepared statements
 - Escape SQL characters from user input

### Risk Analysis 2

| Frequency | Effect |
|----------:|-------:|
|         0 |      0 |

---
## VII- XSS (Reflected)

This breach will allow hacker to alter the html file and have a javascript run in his own browser. He could then use social engineering to spread the link (such as `http://192.168.10.100/vulnerabilities/xss_r/?name=%3Cscript%3E++%09let+test+%3D+%22test%22%3B+%09while%281%29+%7B+%09alert%28test%29%3B+%7D+%3C%2Fscript%3E#`) to unaware users.

![xss-r.png](/images/xss-r.png)
![xss-r-code.png](/images/xss-r-code.png)

### Attack story

A spam mail asks a victim to pay its energy bill using an infected link of the vulnerable website.

### Risk Analysis

| Frequency | Effect |
|----------:|-------:|
|         1 |      2 |

### Recommendation

 - Escape script tags from user input
 - Sensitize users not to click on links from mails

### Risk Analysis 2

| Frequency | Effect |
|----------:|-------:|
|         0 |      0 |

---
## VIII- XSS (Stored)

This is very critical because the hacker can have a script ran by any visitor of the infected website.

![xss-s.png](/images/xss-s.png)
![xss-s2.png](/images/xss-s2.png)

### Attack story

A malicious user creates a script to use a visitor's processor to mine cryptocurrencies on a forum.

### Risk Analysis

| Frequency | Effect |
|----------:|-------:|
|         2 |      3 |

### Recommendation

 - Escape script tags from user input before saving it in the server databases

### Risk Analysis 2

| Frequency | Effect |
|----------:|-------:|
|         0 |      0 |
