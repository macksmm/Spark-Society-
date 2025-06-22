community-platform/
├── backend/
│   ├── server.js
│   ├── models/
│   ├── routes/
│   ├── controllers/
│   └── middleware/
└── frontend/
    └── (React App)
    const express = require('express');
    const mongoose = require('mongoose');
    const cors = require('cors');
    require('dotenv').config();

    const app = express();
    app.use(cors());
    app.use(express.json());

    // Import Routes
    const authRoutes = require('./routes/auth');
    const communityRoutes = require('./routes/community');

    app.use('/api/auth', authRoutes);
    app.use('/api/community', communityRoutes);

    mongoose.connect(process.env.MONGO_URI)
      .then(() => app.listen(5000, () => console.log('Server running on port 5000')))
        .catch((err) => console.error(err));
        const mongoose = require('mongoose');

        const userSchema = new mongoose.Schema({
          username: String,
            email: String,
              password: String,
                role: { type: String, enum: ['member', 'admin'], default: 'member' },
                });

                module.exports = mongoose.model('User', userSchema);
               const mongoose = require('mongoose');

               const communitySchema = new mongoose.Schema({
                 name: String,
                   description: String,
                     channels: [{ name: String, messages: [{ user: String, content: String }] }],
                       members: [{ type: mongoose.Schema.Types.ObjectId, ref: 'User' }],
                         createdBy: { type: mongoose.Schema.Types.ObjectId, ref: 'User' },
                         });

                         module.exports = mongoose.model('Community', communitySchema);
                         const express = require('express');
                         const jwt = require('jsonwebtoken');
                         const bcrypt = require('bcryptjs');
                         const User = require('../models/User');
                         const router = express.Router();

                         router.post('/signup', async (req, res) => {
                           const { username, email, password } = req.body;
                             const hashed = await bcrypt.hash(password, 10);
                               const user = await User.create({ username, email, password: hashed });
                                 res.json(user);
                                 });

                                 router.post('/login', async (req, res) => {
                                   const user = await User.findOne({ email: req.body.email });
                                     const valid = await bcrypt.compare(req.body.password, user.password);
                                       if (!valid) return res.status(400).json({ message: 'Invalid credentials' });

                                         const token = jwt.sign({ id: user._id, role: user.role }, process.env.JWT_SECRET);
                                           res.json({ token, user });
                                           });

                                           module.exports = router; 
                                           