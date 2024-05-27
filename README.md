### **Understanding Computer Systems through the File Upload Web Application**

---

**Introduction**
- **Goal**: To understand the different aspects of computer systems—hardware, software, networks, and security—through the implementation of a file upload web application.

---

### **Hardware**

**Structure and Operation**
- **Raspberry Pi** (or any server):
  - **CPU**: Executes instructions and processes data.
  - **RAM**: Temporarily stores data and instructions that the CPU needs while executing tasks.
  - **Storage**: Persistent storage for the operating system, application files, and uploaded files (e.g., SD card, SSD).
  - **Network Interfaces**: Ethernet or Wi-Fi to connect to the local network or the internet.
  - **I/O Ports**: Interfaces for peripherals like keyboards, mice, and external storage devices.

**Role in the Project**
- **Hosting the Server**: The Raspberry Pi runs the web server, processes file uploads, and serves web pages to users.
- **Storing Uploaded Files**: The uploaded files are saved to the storage of the Raspberry Pi.

---

### **Software**

**Structure and Operation**
- **Operating System**: Typically a Linux-based OS (e.g., Raspbian) that manages hardware resources and provides a platform for running applications.
- **Node.js**: JavaScript runtime environment that executes JavaScript code outside a web browser.
- **Express.js**: Web application framework for Node.js, used to create the server and handle routing.
- **Multer**: Middleware for handling multipart/form-data, used for processing file uploads.
- **dotenv**: Library to load environment variables from a `.env` file into `process.env`.

**Code Implementation**
- **Loading Environment Variables**:
  ```js
  require('dotenv').config();
  ```
  - **Purpose**: Load sensitive data like session secrets and user credentials from a `.env` file.

- **Session Management**:
  ```js
  const session = require('express-session');
  app.use(session({
    secret: process.env.SESSION_SECRET,
    resave: false,
    saveUninitialized: true
  }));
  ```
  - **Purpose**: Manage user sessions to keep track of logged-in users.

- **Parsing Form Data**:
  ```js
  const bodyParser = require('body-parser');
  app.use(bodyParser.urlencoded({ extended: true }));
  ```
  - **Purpose**: Parse form data sent in POST requests.

- **Serving Static Files**:
  ```js
  app.use(express.static('public'));
  ```
  - **Purpose**: Serve HTML and CSS files from the `public` directory.

- **User Authentication**:
  ```js
  function isAuthenticated(req, res, next) {
    if (req.session.user) {
      return next();
    } else {
      res.redirect('/login');
    }
  }
  ```
  - **Purpose**: Ensure only authenticated users can access certain routes.

- **File Upload Handling**:
  ```js
  const multer = require('multer');
  const storage = multer.diskStorage({
    destination: (req, file, cb) => {
      const uploadDir = 'uploads';
      if (!fs.existsSync(uploadDir)) {
        fs.mkdirSync(uploadDir);
      }
      cb(null, uploadDir);
    },
    filename: (req, file, cb) => {
      cb(null, Date.now() + path.extname(file.originalname));
    }
  });
  const upload = multer({ storage: storage });
  ```
  - **Purpose**: Configure Multer to store uploaded files in the `uploads` directory with unique filenames.

---

### **Networks**

**Structure and Operation**
- **Local Area Network (LAN)**: The Raspberry Pi connects to a local network via Ethernet or Wi-Fi, allowing devices within the same network to communicate.
- **Internet**: The Raspberry Pi can also be accessed remotely over the internet if configured with proper network settings.
- **TCP/IP Protocol**: Fundamental suite of protocols that allow devices to communicate over the network.
- **HTTP/HTTPS Protocols**: Used for transferring web pages and data between the server and clients.

**Role in the Project**
- **Communication**: The server and clients communicate over the network using HTTP to handle requests and responses for file uploads, listing files, and serving web pages.
- **Accessing the Web Application**: Users access the web application through a web browser by connecting to the server's IP address and port.

---

### **Security**

**Structure and Operation**
- **Session Management**: Ensures that only authenticated users can access certain routes.
- **Environment Variables**: Used to securely store sensitive information such as session secrets and user credentials.
- **File Handling**: Properly configured to prevent file overwriting and ensure that files are stored securely.
- **Input Validation**: Ensures that the data received from users is valid and prevents potential security issues such as injection attacks.

**Code Implementation**
- **Session Secret**:
  ```js
  secret: process.env.SESSION_SECRET
  ```
  - **Purpose**: Used to sign the session ID cookie, ensuring it cannot be tampered with.

- **User Credentials**:
  ```js
  const { username, password } = req.body;
  if (username === process.env.USER_NAME && password === process.env.USER_PASSWORD) {
    req.session.user = username;
    res.redirect('/');
  } else {
    res.send('Invalid username or password. <a href="/login">Try again</a>');
  }
  ```
  - **Purpose**: Validates user credentials against those stored in environment variables.

- **File Permissions**: Ensures that only authenticated users can upload, view, or delete files.

---

**Summary**
- **Hardware**: The Raspberry Pi acts as the physical server, running the web application and storing uploaded files.
- **Software**: Node.js and Express.js handle server-side operations, with Multer for file uploads and dotenv for managing environment variables.
- **Networks**: The application uses TCP/IP and HTTP/HTTPS protocols to communicate over the local network or internet.
- **Security**: Implemented through session management, environment variables, proper file handling, and input validation.

---


### **Step-by-Step Notes: Simple File Upload Web Application**

---

**Part 1: Introduction**
- **Title**: Simple File Upload Web Application
- **Overview**: This is a web application that allows users to upload files, view them, and delete them. The following will cover the user login, file handling, and basic web server setup used.

---

**Part 2: Project Structure**
- **Directory Layout**:
  ```
  file-upload-server/
  ├── public/
  │   ├── css/
  │   │   └── styles.css
  │   ├── index.html
  │   └── login.html
  ├── uploads/
  ├── .env
  ├── package.json
  └── server.js
  ```
  - **public/**: Contains HTML and CSS files.
  - **uploads/**: Stores uploaded files.
  - **.env**: Environment variables for sensitive data.
  - **package.json**: Project dependencies.
  - **server.js**: Main server-side code.

---

**Part 3: Setting Up Environment Variables**
- **.env File**:
  ```sh
  SESSION_SECRET=your_strong_secret_key
  USER_NAME=user
  USER_PASSWORD=password
  ```
  - **Purpose**: Store sensitive information securely.

---

**Part 4: Loading Environment Variables**
- **dotenv Package**:
  ```js
  require('dotenv').config();
  ```
  - **Explanation**: Loads variables from `.env` file into `process.env`.

---

**Part 5: Managing Sessions**
- **Session Middleware**:
  ```js
  const session = require('express-session');
  app.use(session({
    secret: process.env.SESSION_SECRET,
    resave: false,
    saveUninitialized: true
  }));
  ```
  - **Purpose**: Manage user sessions securely.

---

**Part 6: Parsing Form Data**
- **Body-Parser Middleware**:
  ```js
  const bodyParser = require('body-parser');
  app.use(bodyParser.urlencoded({ extended: true }));
  ```
  - **Purpose**: Parse form data from requests.

---

**Part 7: Serving Static Files**
- **Static Middleware**:
  ```js
  const express = require('express');
  app.use(express.static('public'));
  ```
  - **Purpose**: Serve HTML and CSS files from the `public` directory.

---

**Part 8: User Authentication Middleware**
- **isAuthenticated Function**:
  ```js
  function isAuthenticated(req, res, next) {
    if (req.session.user) {
      return next();
    } else {
      res.redirect('/login');
    }
  }
  ```
  - **Purpose**: Ensure users are logged in before accessing certain pages.

---

**Part 9: Login Page**
- **HTML Form**:
  ```html
  <!-- login.html -->
  <form action="/login" method="post">
    <label for="username">Username:</label>
    <input type="text" id="username" name="username" required>
    <br>
    <label for="password">Password:</label>
    <input type="password" id="password" name="password" required>
    <br>
    <input type="submit" value="Login">
  </form>
  ```
  - **Purpose**: Form for users to log in.

---

**Part 10: Handling Login Submission**
- **Login Route**:
  ```js
  app.post('/login', (req, res) => {
    const { username, password } = req.body;
    if (username === process.env.USER_NAME && password === process.env.USER_PASSWORD) {
      req.session.user = username;
      res.redirect('/');
    } else {
      res.send('Invalid username or password. <a href="/login">Try again</a>');
    }
  });
  ```
  - **Purpose**: Validate user credentials and create a session.

---

**Part 11: Handling Logout**
- **Logout Route**:
  ```js
  app.get('/logout', (req, res) => {
    req.session.destroy();
    res.redirect('/login');
  });
  ```
  - **Purpose**: Destroy the user session and redirect to the login page.

---

**Part 12: Upload Page**
- **HTML Form**:
  ```html
  <!-- index.html -->
  <form action="/upload" method="post" enctype="multipart/form-data">
    <input type="file" name="file" required>
    <input type="submit" value="Upload">
  </form>
  <a href="/list"><button>List All Files</button></a>
  <a href="/logout"><button>Logout</button></a>
  ```
  - **Purpose**: Form for users to upload files.

---

**Part 13: Handling File Uploads**
- **Multer Configuration**:
  ```js
  const multer = require('multer');
  const storage = multer.diskStorage({
    destination: (req, file, cb) => {
      const uploadDir = 'uploads';
      if (!fs.existsSync(uploadDir)) {
        fs.mkdirSync(uploadDir);
      }
      cb(null, uploadDir);
    },
    filename: (req, file, cb) => {
      cb(null, Date.now() + path.extname(file.originalname));
    }
  });
  const upload = multer({ storage: storage });
  ```
  - **Purpose**: Configure file storage and handling.

---

**Part 14: Upload Route**
- **Upload Route**:
  ```js
  app.post('/upload', isAuthenticated, upload.single('file'), (req, res) => {
    if (!req.file) {
      return res.status(400).send('No file uploaded.');
    }
    res.send(`
      <html>
        <body>
          <h1>File uploaded successfully.</h1>
          <a href="/uploads/${req.file.filename}">View Uploaded File</a>
          <br><br>
          <a href="/">Upload More Files</a>
          <br><br>
          <a href="/list">List All Files</a>
          <br><br>
          <a href="/logout">Logout</a>
        </body>
      </html>
    `);
  });
  ```
  - **Purpose**: Handle file uploads and respond with success message and links.

---

**Part 15: Serving Uploaded Files**
- **Static Middleware**:
  ```js
  app.use('/uploads', express.static('uploads'));
  ```
  - **Purpose**: Serve uploaded files for viewing and downloading.

---

**Part 16: Listing Uploaded Files**
- **List Files Route**:
  ```js
  app.get('/list', isAuthenticated, (req, res) => {
    fs.readdir('uploads', (err, files) => {
      if (err) {
        return res.status(500).send('Unable to list files.');
      }
      const fileList = files.map(file => `
        <li>
          <a href="/uploads/${file}" download>${file}</a>
          <button onclick="deleteFile('${file}')">Delete</button>
        </li>
      `).join('');
      res.send(`
        <html>
          <body>
            <h1>List of Uploaded Files</h1>
            <ul>${fileList}</ul>
            <br>
            <a href="/">Back to Upload Page</a>
            <a href="/logout">Logout</a>
            <script>
              function deleteFile(fileName) {
                fetch('/delete/' + fileName, { method: 'DELETE' })
                  .then(response => response.text())
                  .then(data => {
                    alert(data);
                    window.location.reload();
                  })
                  .catch(err => console.error(err));
              }
            </script>
          </body>
        </html>
      `);
    });
  });
  ```
  - **Purpose**: List uploaded files with download and delete options.

---

**Part 17: Deleting Files**
- **Delete File Route**:
  ```js
  app.delete('/delete/:filename', isAuthenticated, (req, res) => {
    const filename = req.params.filename;
    const filePath = path.join('uploads', filename);
    fs.unlink(filePath, (err) => {
      if (err) {
        return res.status(500).send('Unable to delete file.');
      }
      res.send('File deleted successfully.');
    });
  });
  ```
  - **Purpose**: Handle file deletion requests.

---

**Part 18: Starting the Server**
- **Server Start Code**:
  ```js
  app.listen(PORT, () => {
    console.log(`Server is running on http://localhost:${PORT}`);
  });
  ```
  - **Purpose**: Start the server and listen for requests on port 3000.

---

**Part 19: Summary**
- **Key Points**:
  - User authentication using sessions.
  - Securely handling file uploads with Multer.
  - Serving static files and managing uploaded files.
  - Using environment variables for sensitive data.

---

These are the core concepts and functionality of the file upload web application.
