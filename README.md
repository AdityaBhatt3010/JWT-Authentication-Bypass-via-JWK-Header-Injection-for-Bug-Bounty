# JWT Authentication Bypass via JWK Header Injection — Burp Suite Lab Writeup 🔐🕵️‍♂️

**Introduction** ✨

JSON Web Tokens (JWT) are widely used for session handling and authentication in modern web applications. While JWTs provide a compact and secure way to transmit information between parties, improper implementation or validation can lead to critical vulnerabilities. One such vulnerability arises when a server accepts a public key embedded directly within the JWT header (via the `jwk` parameter) without verifying the trustworthiness of that key. ⚠️

This lab demonstrates how an attacker can craft a malicious JWT containing a custom JSON Web Key (JWK) in the header, tricking the server into validating the token with a public key controlled by the attacker. This flaw enables unauthorized access to restricted resources, such as the admin panel, and the ability to perform privileged actions like deleting users. 💥

![Cover](https://github.com/user-attachments/assets/a9d1feef-0782-4ed6-ad0c-80f554a5029c) <br/>


---

## Lab Details 📝

* **Lab:** JWT authentication bypass via jwk header injection
* **Objective:** Modify and sign a JWT to gain admin access and delete the user `carlos` 🗡️
* **Credentials:** wiener\:peter
* **Key Insight:** The server fails to verify that the embedded `jwk` comes from a trusted source, allowing key substitution attacks. 🔑

---

## Step-by-Step Exploitation (PoC) 🛠️

### Background: Install JWT Editor Extension in Burp Suite 🔧

![JWTEditor](https://github.com/user-attachments/assets/f3eb6f7a-2aa8-4eb3-a297-5b8b4572f835) <br/>

1. Access the lab URL:
   `https://portswigger.net/web-security/jwt/lab-jwt-authentication-bypass-via-jwk-header-injection` and sign in with credentials:
   `wiener:peter` 👤

   ![1](https://github.com/user-attachments/assets/2e2a2a4c-d889-453f-8f8c-8db1cfff048b) <br/>

2. Attempt to access the admin panel at `/admin`. The response shows:
   *“Admin interface only available if logged in as an administrator.”* 🚫

   ![2](https://github.com/user-attachments/assets/95d6da1b-f399-41c3-8f73-097eb69a486c) <br/>

3. Capture the `/admin` request and send it to Burp Repeater for further manipulation.

  ![3](https://github.com/user-attachments/assets/6c61ea10-f0ea-4e2c-9646-f2880e9f7c01) <br/>

4. Open the JWT Editor tab within Burp Suite. Create a new RSA key by clicking **New RSA Key** and then **Generate** to produce a fresh key pair. 🔑✨

  ![4](https://github.com/user-attachments/assets/012829fb-385b-4882-8969-b65609147fa6) <br/>

5. Return to Burp Repeater and open the **JSON Web Token** tab. Modify the token payload by changing the `sub` claim from `wiener` to `administrator`. Click **Attack**. 🎯

  ![5](https://github.com/user-attachments/assets/4c9fe1f6-446f-447e-be59-229c8cc0d818) <br/>
  
6. Select the **Embedded JWK** attack option.

  ![6](https://github.com/user-attachments/assets/0f10461d-fd3d-4cb4-adbe-146591b985d6) <br/>
  
7. Choose the newly generated RSA key to embed in the JWT header.

  ![7](https://github.com/user-attachments/assets/81ead8ec-2472-4bed-8057-3b632c9cd666) <br/>

8. Notice that the JWT header now includes a `jwk` parameter containing your public key. 🧩

  ![8](https://github.com/user-attachments/assets/1c86c42d-30a9-45ab-8875-4dc30065e66c) <br/>

9. Send the modified request. You now have access to the admin panel, bypassing authentication checks. 🚪🛡️

  ![9](https://github.com/user-attachments/assets/760d3c20-8ac3-4f53-88c6-01b8d771eb34) <br/>

10. Change the request URL to `/admin/delete?username=carlos` to delete the user `carlos`, and send the request. 💀

  ![10](https://github.com/user-attachments/assets/65d4111d-42b9-4591-a469-2313943bbc80) <br/>
  
11. Right-click the request and select **Show in Browser**. Copy and paste the URL into your browser to confirm the lab is solved. 🎉 Congratulations!

  ![11](https://github.com/user-attachments/assets/cc0eb9ca-8a9f-446c-b3a6-725945506ede) <br/>
  
---

## Explanation 🧠

This vulnerability exists because the server trusts the public key provided within the JWT header's `jwk` parameter without verifying its authenticity. By generating your own RSA key pair and embedding the public key in the token, you can sign the JWT with the corresponding private key, which the server then accepts as valid. This trick allows attackers to impersonate privileged users and bypass normal authentication flows. 🎭

---

## Mitigation Recommendations 🛡️

* Do **not** trust keys embedded within JWT headers from untrusted sources.
* Enforce strict validation of JWT signing keys against known, trusted keys stored securely on the server. 🔒
* Avoid supporting dynamic key embedding in tokens unless combined with robust trust verification mechanisms.
* Employ additional authorization checks on sensitive endpoints like `/admin`. 🚨

---

## Happy Hacking! 🎉👾

JWTs are powerful but tricky. Always dig deep into token validation logic when testing authentication. This lab is a prime example of how subtle implementation flaws can lead to serious privilege escalation.

Keep pushing the boundaries of your security skills — and as always, happy hacking! 🗿🔐

---

If you want me to format this for Medium or any other blog, just say the word!
