SMART LIBRARY MANAGEMENT SYSTEM — FIREBASE STARTER

This package keeps the redesigned library website and connects the Student Registration form to Cloud Firestore. It is a starter: you must paste your exact Firebase Web API key and publish the Firestore Rules below before submissions will work.

A. SET YOUR WEB CONFIG
1. Open index.html in a text editor.
2. Find PASTE_YOUR_FIREBASE_API_KEY_HERE.
3. In Firebase Console > Project settings > Your apps > SDK setup and configuration, choose Config and copy the exact apiKey value into index.html. The other project values are prefilled from the project shown in your screenshots; verify them against the console.
4. Do not paste a service-account/private key into this file. Firebase web config is client-side config, not a server secret.

B. SET FIRESTORE RULES
Firebase Console > Firestore Database > Rules. Replace the current rules with the following and click Publish:

rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /students/{studentId} {
      allow create: if
        request.resource.data.keys().hasAll(['name','email','mobile','course','gender','skills','address','createdAt']) &&
        request.resource.data.keys().hasOnly(['name','email','mobile','course','gender','skills','address','createdAt']) &&
        request.resource.data.name is string && request.resource.data.name.size() >= 2 && request.resource.data.name.size() <= 80 &&
        request.resource.data.email is string && request.resource.data.email.size() >= 5 && request.resource.data.email.size() <= 254 &&
        request.resource.data.mobile is string && (request.resource.data.mobile == '' || request.resource.data.mobile.matches('^[0-9]{10}$')) &&
        request.resource.data.course in ['BCA','MCA','B.Tech','MBA'] &&
        request.resource.data.gender in ['', 'Male', 'Female'] &&
        request.resource.data.skills is list && request.resource.data.skills.size() <= 4 &&
        request.resource.data.address is string && request.resource.data.address.size() <= 1000 &&
        request.resource.data.createdAt == request.time;
      allow read, update, delete: if false;
    }
  }
}

Important security limitation: the public form is unauthenticated. These rules allow anyone to submit a new, schema-valid registration, but prevent clients from reading, changing, or deleting records. Rules validation is not full spam/bot protection. For a public production site, add a trusted server-side submission endpoint with rate limiting and CAPTCHA/App Check before collecting real personal data. Never use open read rules or allow read, write: if true.

C. TEST
1. Upload index.html to the ROOT of your GitHub Pages repository, replacing the existing index.html.
2. Wait for GitHub Pages to redeploy.
3. Submit a test registration.
4. Firebase Console > Firestore Database > Data should show a students collection.
5. If it fails, check browser console and confirm the apiKey/projectId and Rules were published.

This form collects personal data (email, mobile and address). For a real public site, collect only what you need and provide a privacy notice.
