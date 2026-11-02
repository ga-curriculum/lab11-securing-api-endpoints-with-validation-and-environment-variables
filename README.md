## **Lab: Securing API Endpoints with Validation and Environment Variables**

[Workflow](./Securing%20API%20endpoints%20with%20validation.png)

**Prerequisites:**

* Basic knowledge of Node.js and Express
* Understanding of REST API endpoints
* Familiarity with `npm` and installing packages

---

### **Lab Objectives**

By the end of this lab, you will be able to:

1. Implement input validation for API endpoints using `Joi` or `express-validator`.
2. Secure sensitive data by using environment variables with `dotenv`.
3. Test the API to verify validation and environmental configurations work correctly.

---

### **Lab Setup**

1. Create a new Node.js project:

   ```bash
   mkdir secure-api-lab
   cd secure-api-lab
   npm init -y
   ```
2. Install required packages:

   ```bash
   npm install express dotenv joi
   ```
3. Create the project structure:

   ```
   secure-api-lab/
   ├─ index.js
   ├─ routes/
   │  └─ userRoutes.js
   ├─ .env
   └─ package.json
   ```

---

### **Part 1: Setup a Basic Express API**

1. **index.js**

   ```javascript
   const express = require('express');
   const dotenv = require('dotenv');
   const userRoutes = require('./routes/userRoutes');

   dotenv.config();
   const app = express();
   const PORT = process.env.PORT || 3000;

   app.use(express.json());
   app.use('/api/users', userRoutes);

   app.listen(PORT, () => console.log(`Server running on port ${PORT}`));
   ```

2. **routes/userRoutes.js**

   ```javascript
   const express = require('express');
   const router = express.Router();

   router.post('/', (req, res) => {
     res.json({ message: 'User endpoint works!' });
   });

   module.exports = router;
   ```

3. Run the server and test using `curl` or Postman:

   ```bash
   node index.js
   ```

---

### **Part 2: Implement Input Validation**

1. Install `Joi` for input validation (already installed above).

2. Update `userRoutes.js`:

   ```javascript
   const express = require('express');
   const Joi = require('joi');
   const router = express.Router();

   // Validation schema
   const userSchema = Joi.object({
     name: Joi.string().min(3).required(),
     email: Joi.string().email().required(),
     password: Joi.string().min(6).required()
   });

   router.post('/', (req, res) => {
     const { error } = userSchema.validate(req.body);
     if (error) {
       return res.status(400).json({ error: error.details[0].message });
     }

     res.json({ message: 'User created successfully!', data: req.body });
   });

   module.exports = router;
   ```

3. **Exercise:** Test the endpoint with valid and invalid inputs using Postman or `curl`. Observe how invalid requests are rejected.

---

### **Part 3: Use Environment Variables to Secure Sensitive Data (15 minutes)**

1. Create a `.env` file in the project root:

   ```
   PORT=5000
   API_SECRET_KEY=mySuperSecretKey
   ```

2. Update `index.js` to use the environment variable:

   ```javascript
   const PORT = process.env.PORT || 3000;
   ```

3. Update `userRoutes.js` to demonstrate using a secret key:

   ```javascript
   const secretKey = process.env.API_SECRET_KEY;

   router.post('/', (req, res) => {
     const { error } = userSchema.validate(req.body);
     if (error) {
       return res.status(400).json({ error: error.details[0].message });
     }

     res.json({ 
       message: 'User created successfully!', 
       data: req.body, 
       secret: secretKey 
     });
   });
   ```

4. **Exercise:** Change the `.env` value and restart the server to observe changes in the response.

---

### **Part 4: Lab Challenge**

* **Task 1:** Add validation for an optional `age` field (must be an integer ≥ 18 if provided).
* **Task 2:** Add a `GET /api/users/secret` endpoint that returns only the `API_SECRET_KEY` if a correct query parameter `key` matches the secret.

---

### **Lab Submission**

* Submit a `.zip` file of the project folder containing:

  * `index.js`
  * `routes/userRoutes.js`
  * `.env.example` (without actual secret)
  * `package.json`
