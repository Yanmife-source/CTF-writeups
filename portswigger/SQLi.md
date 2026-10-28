# SQLI (Structured Query Language Injections)
A write-up series documenting my journey through the Portswigger web academy SQLi labs

**Goal:** To create a payload that exploits the vuln as the application doesn't have any defenses against SQLi attacks

## SQL injection vulnerability in WHERE clause allowing retrieval of hidden data (Apprentice level)
The application is a shopping application that displays products in different categories. When the user clicks on the Gifts category their browser requests the URL: 
`https://insecure-website.com/products?category=Gifts`
This causes the application to make a SQL query to retrieve details of the relevant products from the database:
`SELECT * FROM products WHERE category = 'Gifts' AND released = 1` and if payloads like `'--` is injected, all  products are displayed irrespective of whether they had been released,
A similar attack can be used to cause the application to display all the products in any category, including categories that they don't know about.This can be done by injecting the payload `' OR 1=1 --`

**Locate where the payload can be injected:**The payload can be injected directly into the URL as `https://insecure-website.com/products?category=Gifts'+OR+1=1--`
* **Result:** This results in SQL query `SELECT * FROM products WHERE category = 'Gifts' OR 1=1--' AND released = 1`
The modified query returns all items where either the category is Gifts, or 1 is equal to 1. As 1=1 is always true, the query returns all items.

---

## SQL injection vulnerability allowing login bypass (Apprentice level)
The application used here is the same on used in the labs above  and this lab contains a SQL injection vulnerability in the login function and to solve this lab, perform a SQL injection attack that logs in to the application as the administrator user.
The payload that is crafted to gain elevated priviledges is `administrator' --` which is entered into the username field of the login page 
1. **Locate where the payload can be injected:**The crafted payload can be injected directly into the login username field
* **Enter the crafted payload into vulnerable field:** After entering the payload into the username field and entering the password as its required to submit the details
* **Result:**After submtting the details, elevated/priviledged access is gained as administator

---

## SQL injection attack, querying the database type and version on Oracle(Practitioner level)
The application used here is the same on used in the labs above  and this lab contains a SQL injection vulnerability in the product category filter.in the URL `https://insecure-website.com/products?category=Lifestyle`
A UNION attack must be used to get the  database type and version on Oracle from the injected query.and it must be carefully crafted as the databse type and number of columns in the database but as it is an Oracle DB.the payload `' UNION SELECT banner, NULL FROM v$version--` can be injected into the SQLi vulnerable field

1. **Locate where the payload can be injected:**The payload can be injected directly into the URL as `https://0a29002e04b7566481e1073d00550058.web-security-academy.net/filter?category=Tech+gifts' UNION SELECT banner, NULL FROM v$version--`
2. **Find the number of columns:** Using `ORDER BY` which is a method which uses trial and error to find the number of columns based of whether or not an error is raised when injected and it is observed that after `ORDER BY 2`,it continues to raise an error
* **Result:** This results in the SQL query requesting for the DB type and version and they are thus printed in the webpage after reloading beginning with line `CORE 11.2.0.2.0 Production` and the rest scattered in the rest of the page

`
