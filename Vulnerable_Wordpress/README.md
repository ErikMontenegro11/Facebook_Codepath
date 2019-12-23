# Project 7 - WordPress Pentesting

Time Spent: 15 Hrs.

# Pentesting Report

## XSS 
- [ ] Summary: An unauthenticated attacker can inject JavaScript in WordPress comments. The script is triggered when the comment is viewed.
    - Vulnerability type: Persistent Cross Site Scripting
    - CVE-2015-3440
   
- [ ] GIF Walkthrough:
    ![](https://github.com/ErikMontenegro11/codepathWPD/blob/master/xss1gif.gif)
    
    ![](https://github.com/ErikMontenegro11/codepathWPD/blob/master/xss2.gif)
    
    ![](https://github.com/ErikMontenegro11/codepathWPD/blob/master/xss3.gif)
    
    ![](https://github.com/ErikMontenegro11/codepathWPD/blob/master/xss4.gif)

- [ ] Steps to recreate: 

1. Obtain the information and documentation of a known XSS in WordPress 4.2 through the exploit database in the following         link: [Exploit Database]:https://www.exploit-db.com/exploits/36844

2. Proceeded to open the webpage in an ingognito browser widow to mimic a user interacting with the page. I then proceeded to make a post on the page as 'Michael Scott' and waited for admin approval for the comment to be posted. (Shown Below)

![](https://github.com/ErikMontenegro11/codepathWPD/blob/master/step1xss.png)

3. After the admin approves the comment, 'Michael Scott' is now able to make any comments to the page without any sort of approval.  At the bottom of the page, we can also see that comment text can be crafted using HTML attributes.  As specified through the documentation in Exploit Database, we can use the provided template to create MySQL code that is large enough to cause truncation.

![](https://github.com/ErikMontenegro11/codepathWPD/blob/master/step2xss.png)

4. After the MySQL code is posted as a comment to the page, the "Hello World" payload appears at the top of the webpage.  Even after leaving the page and coming back to it, the code continues to execute automatically.

![](https://github.com/ErikMontenegro11/codepathWPD/blob/master/step3xss.png)


# User Enumeration (Two Methods)

- [ ] Summary: Half the battle in gaining access to a system is knowing the appropriate usernames to attack.  User enumeration is a one technique we can use in the information gathering process.  In wordpress 4.2, slightly altering the url can return back the name of the person who is the 'author' of that page.  We can also use wpscan in Kali Linux to enumerate usernames.  Notice that with the url method, the authors legal name as it's stored in the website is returned.  With wpscan in Kali, the true log-in username of those authors is returned so we actually retrieved two pieces of information. 

- [ ] GIF Walkthrough:

    ![](https://github.com/ErikMontenegro11/codepathWPD/blob/master/ue1.gif)
    
    ![](https://github.com/ErikMontenegro11/codepathWPD/blob/master/ue2.gif)
    
    ![](https://github.com/ErikMontenegro11/codepathWPD/blob/master/ue3.gif)

- [ ] Steps to recreate:

1. On a permalink-enabled site, adding the '?author=n' (where n is an integer) to the end of the request redirects to a permalink version of the URL for that user, which by default returns the author's name.  To see this, I sent the following modified requests to the webpage and obtained the subsequent responses:

http://<i></i>wpdistillery.vm/?author=1
![](https://github.com/ErikMontenegro11/codepathWPD/blob/master/enum1.png)

http://<i></i>wpdistillery.vm/?author=2
![](https://github.com/ErikMontenegro11/codepathWPD/blob/master/enum2.png)

http://<i></i>wpdistillery.vm/?author=3
![](https://github.com/ErikMontenegro11/codepathWPD/blob/master/enum3.png)


2. For the second method, I used a wpscan in Kali to enumerate users using the following command:
    wpscan --url wpdistillery.vm -e u    
    Where:'--url' specifices the url of the target & '-e u' tells kali to enumerate usernames.
    
![](https://github.com/ErikMontenegro11/codepathWPD/blob/master/wpscan1.png)

The Results of the scan:
![](https://github.com/ErikMontenegro11/codepathWPD/blob/master/wpscan2.png)
    

# CSRF

- [ ] Summary: When posting comments, WordPress 4.2 does not require a nonce value (unless posting unfiltered HTML). This means that an attacker can force a logged-in user to post arbitrary comments.  In order to execute this attack, a logged-in user would have to click on a link to a website controlled by the attacker.  We can use GitHub to host a page running a malicious form in HTML that will execute whenever the page is visited.

  - Discovered by: [Mallory Adams and dxwSupport]:https://advisories.dxw.com/advisories/comment-form-csrf-allows-admin-impersonation-via-comments-in-wordpress-4-2-2/
  - Advisory ID: dxw-1970-1086
  - CVE: Awaiting assignment

- [ ] GIF Walkthrough:
  
  ![](https://github.com/ErikMontenegro11/codepathWPD/blob/master/csrf1.gif)
  Hide attacker page behind href anchor text
  ![](https://github.com/ErikMontenegro11/codepathWPD/blob/master/csrf2.gif)
  Link clicked while Admin is logged in and form is submitted
  ![](https://github.com/ErikMontenegro11/codepathWPD/blob/master/csrf3.gif)
  

- [ ] Steps to Recreate:

1. Started by adding a comment to a page while being logged in as Admin
![](https://github.com/ErikMontenegro11/codepathWPD/blob/master/csrf1.png)
![](https://github.com/ErikMontenegro11/codepathWPD/blob/master/csrf2.png)

2. I have hosted a webpage on Github that, when visited, executes the form.  
  * "comment" is the string we want to post in the comment (What we want it to say)
  * "comment_post_id" is the post number that we want to post to.  We can verify this in the url in the image above. (p=21)
  * "comment_parent = 0" tells the code to post as a new comment.
  * " wp_unfiltered_html_comment" - The value of this variable is just a random unique 'ID' number for the comment
  
![](https://github.com/ErikMontenegro11/codepathWPD/blob/master/csrf3.png)

3. We can hide the url to the attacker site inside of an "href" and attempt to conceal the true url by hiding behind the anchor text that will appear in the comment.  In this case, "Redirect to CSRF" is the anchor text.  Clicking the link will redirect the logged-in user to the attackers webpage.
![](https://github.com/ErikMontenegro11/codepathWPD/blob/master/csrf4.png)
![](https://github.com/ErikMontenegro11/codepathWPD/blob/master/csrf5.png)

4. Because 'Michael Scott' has already posted to the site before with admin approval, nothing else he posts will require the admin the review his post.
![](https://github.com/ErikMontenegro11/codepathWPD/blob/master/csrf6.png)

5. The logged-in admin clicks on the redirect link, the csrfForm executes and submits, and the post 'You've beeen CSRFed' is posted to the webpage and appears as though the admin was the person who created the post.
![](https://github.com/ErikMontenegro11/codepathWPD/blob/master/csrf7.png)
