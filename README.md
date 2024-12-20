Social media
const express = require('express');
const mongoose = require('mongoose');
const bodyParser = require('body-parser');
const app = express();
const PORT = process.env.PORT || 3000;

// Middleware
app.use(bodyParser.json());

// MongoDB connection
mongoose.connect('mongodb://localhost/socialPlatform', { useNewUrlParser: true, useUnifiedTopology: true });

// User Schema
const userSchema = new mongoose.Schema({
    username: { type: String, required: true, unique: true },
    password: { type: String, required: true },
    email: { type: String, required: true, unique: true },
    profilePicture: String,
    bio: String,
});

const User = mongoose.model('User', userSchema);

// User Registration
app.post('/register', async (req, res) => {
    const { username, password, email } = req.body;
    const newUser = new User({ username, password, email });
    await newUser.save();
    res.status(201).send('User registered successfully');
});

// User Login
app.post('/login', async (req, res) => {
    const { username, password } = req.body;
    const user = await User.findOne({ username, password });
    if (user) {
        res.status(200).send('Login successful');
    } else {
        res.status(401).send('Invalid credentials');
    }
});

// Privacy Policy Endpoint
app.get('/privacy-policy', (req, res) => {
    const privacyPolicy = `
    ## Privacy Policy

    Your privacy is important to us. This policy outlines how we collect, use, and protect your information.

    - **Information Collection**: We collect information you provide directly, such as your username and email.
    - **Use of Information**: We use your information to provide and improve our services.
    - **Data Protection**: We implement security measures to protect your information.
    - **User Rights**: You have the right to access, correct, or delete your personal information.

    For more details, please contact us.
    `;
    res.send(privacyPolicy);
});

// Start the server
app.listen(PORT, () => {
    console.log(`Server is running on http://localhost:${PORT}`);
});
