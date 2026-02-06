# Face Recognition Entry System

[![Python](https://img.shields.io/badge/Python-3.8%2B-blue.svg)](https://www.python.org/)
[![Flask](https://img.shields.io/badge/Flask-2.0.1-green.svg)](https://flask.palletsprojects.com/)
[![MySQL](https://img.shields.io/badge/MySQL-8.0%2B-orange.svg)](https://www.mysql.com/)
[![License](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

A modern, AI-powered employee attendance management system that leverages advanced face recognition technology to automate and secure employee entry/exit tracking. The system features real-time face detection, anti-spoofing protection, and a comprehensive admin dashboard for efficient workforce management.

---

## Table of Contents

- [Features](#features)
- [Demo](#demo)
- [System Architecture](#system-architecture)
- [Technologies Used](#technologies-used)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Configuration](#configuration)
- [Usage](#usage)
- [Project Structure](#project-structure)
- [API Endpoints](#api-endpoints)
- [Database Schema](#database-schema)
- [Security Features](#security-features)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)
- [License](#license)
- [Acknowledgments](#acknowledgments)
- [Contact](#contact)

---

## Features

### Core Functionality
- **Face Recognition Authentication**: Accurate employee identification using deep learning-based face embeddings
- **Real-Time Processing**: Low-latency face detection and recognition with live video feed
- **Anti-Spoofing Detection**: Multi-model validation to prevent photo/video-based attacks
- **Automated Attendance Tracking**: Automatic logging of employee entry and exit times
- **Admin Dashboard**: Comprehensive interface for employee and attendance management

### Advanced Features
- **Multiple Security Layers**: Password hashing, session management, and rate limiting
- **Flexible Reporting**: Query attendance by date, employee ID, or date ranges
- **User Management**: Add, edit, and remove employees from the system
- **Session Security**: Automatic logout and session timeout protection
- **Scalable Architecture**: Optimized for growing employee databases

---

## Demo

### Employee Workflow
1. **Registration**: Employee registers with name and ID, system captures face
2. **Check-in**: Employee faces camera, system identifies and logs entry time
3. **Check-out**: Employee faces camera again, system logs exit time

### Admin Workflow
1. **Login**: Secure admin authentication with rate limiting
2. **Dashboard**: View all registered employees and attendance records
3. **Reports**: Generate attendance reports by various filters
4. **Management**: Edit or remove employee records

---

## System Architecture

```
┌─────────────────┐
│   Web Browser   │
└────────┬────────┘
         │
    ┌────▼────┐
    │  Flask  │
    │  Server │
    └────┬────┘
         │
    ┌────▼────────────────┐
    │                     │
┌───▼────┐         ┌─────▼─────┐
│ OpenCV │         │   MySQL   │
│ DeepFace│         │ Database  │
└────────┘         └───────────┘
```

**Architecture Components:**
- **Frontend**: HTML/CSS/JavaScript with Bootstrap
- **Backend**: Flask web framework with Python
- **Database**: MySQL for persistent storage
- **AI/ML**: InceptionResNetV2 for face embeddings, DeepFace for analysis
- **Security**: Flask-Bcrypt, Flask-Session, Flask-Limiter

---

## Technologies Used

### Backend
- **Python 3.7.11+**: Core programming language
- **Flask 2.0.1**: Lightweight web framework
- **MySQL Connector**: Database connectivity
- **OpenCV 4.5.3**: Real-time computer vision
- **DeepFace**: Face recognition and analysis
- **TensorFlow 2.6.0**: Deep learning framework
- **Scikit-learn 0.24.2**: Machine learning utilities

### Security & Authentication
- **Flask-Bcrypt**: Password hashing
- **Flask-Session**: Secure session management
- **Flask-Limiter**: Rate limiting protection
- **python-dotenv**: Environment variable management

### AI Models
- **InceptionResNetV2**: Face embedding generation
- **Facenet**: Spoof detection
- **VGG-Face**: Spoof detection
- **OpenFace**: Spoof detection

---

## Prerequisites

Before installation, ensure you have the following:

- **Python 3.8 or higher**
- **MySQL Server 8.0 or higher**
- **Webcam** (for face capture)
- **Git** (for cloning the repository)
- **pip** (Python package manager)

### System Requirements
- **OS**: Windows 10/11, macOS, or Linux
- **RAM**: Minimum 4GB (8GB recommended)
- **Storage**: At least 2GB free space
- **Camera**: HD webcam (720p or higher recommended)

---

## Installation

### Step 1: Clone the Repository

```bash
git clone https://github.com/yourusername/face-recognition-entry-system.git
cd face-recognition-entry-system
```

### Step 2: Create Virtual Environment

```bash
# Windows
python -m venv venv
venv\Scripts\activate

# macOS/Linux
python3 -m venv venv
source venv/bin/activate
```

### Step 3: Install Dependencies

```bash
pip install -r requirements.txt
```

**Note**: If you encounter issues with TensorFlow or OpenCV, install them separately:

```bash
pip install tensorflow==2.6.0
pip install opencv-python==4.5.3.56
```

### Step 4: Download Pre-trained Models

Run the model initialization script:

```bash
python detectin.py
```

This will download required DeepFace models (Facenet, VGG-Face, OpenFace).

### Step 5: Set Up MySQL Database

1. **Install MySQL**: Download from [mysql.com](https://www.mysql.com/)
2. **Create Database**:

```sql
CREATE DATABASE face_recognition_db;
```

3. **Create User** (optional but recommended):

```sql
CREATE USER 'faceapp_user'@'localhost' IDENTIFIED BY 'your_secure_password';
GRANT ALL PRIVILEGES ON face_recognition_db.* TO 'faceapp_user'@'localhost';
FLUSH PRIVILEGES;
```

### Step 6: Configure Environment Variables

Create a `.env` file in the project root:

```bash
# Database Configuration
DB_HOST=localhost
DB_USER=root
DB_PASSWORD=your_mysql_password
DB_NAME=face_recognition

# Security Configuration
SECRET_KEY=your_super_secret_key_here_change_this

# Admin Credentials (Hashed)
PRESET_ADMIN_ID=$2b$12$example_hashed_admin_id_here
PRESET_ADMIN_PASSWORD_HASH=$2b$12$example_hashed_password_here
```

**Generate Admin Credentials**:

```python
from flask_bcrypt import Bcrypt
bcrypt = Bcrypt()

admin_id = "Face_recog_admin"
admin_password = "FRA#987"

hashed_id = bcrypt.generate_password_hash(admin_id).decode('utf-8')
hashed_password = bcrypt.generate_password_hash(admin_password).decode('utf-8')

print(f"PRESET_ADMIN_ID={hashed_id}")
print(f"PRESET_ADMIN_PASSWORD_HASH={hashed_password}")
```

---

## Configuration

### Database Configuration

The application automatically creates required tables on first run. Manual schema:

```sql
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    embedding LONGBLOB,
    employee_id VARCHAR(50) NOT NULL UNIQUE
);

CREATE TABLE employee_entries (
    id INT AUTO_INCREMENT PRIMARY KEY,
    employee_id VARCHAR(50) NOT NULL,
    name VARCHAR(100) NOT NULL,
    entry_time TIME,
    exit_time TIME,
    entry_date DATE
);
```

### Application Configuration

Edit configuration in `app.py`:

```python
# Session timeout (default: 60 minutes)
app.config['PERMANENT_SESSION_LIFETIME'] = timedelta(minutes=60)

# Rate limiting (default: 150 requests per minute)
limiter = Limiter(get_remote_address, app=app, default_limits=["150 per minute"])

# Face similarity threshold (default: 0.75)
similarity > 0.75  # Adjust for stricter/looser matching
```

---

## Usage

### Starting the Application

```bash
python app.py
```

The application will be available at: `http://127.0.0.1:5000/`

### Employee Registration

1. Navigate to `http://127.0.0.1:5000/register`
2. Enter employee name and unique employee ID
3. Position face in camera frame
4. Click "Register" to capture face and create profile

### Employee Check-In

1. Navigate to `http://127.0.0.1:5000/login`
2. Face the camera for recognition
3. System automatically identifies and logs entry time
4. Receive welcome message upon successful check-in

### Employee Check-Out

1. Navigate to `http://127.0.0.1:5000/checkout`
2. Face the camera for recognition
3. System logs exit time for the current date
4. Receive confirmation message

### Admin Access

1. Navigate to `http://127.0.0.1:5000/admin/login`
2. **Default Credentials**:
   - Username: `Face_recog_admin`
   - Password: `FRA#987`
3. Access admin dashboard for full system management

### Admin Features

- **View Users**: See all registered employees
- **Edit User**: Modify employee information
- **Delete User**: Remove individual employees
- **Delete All Users**: Clear entire employee database
- **Attendance Reports**:
  - View by employee ID
  - View by specific date
  - View date ranges
  - Export attendance data

---

## Project Structure

```
face-recognition-entry-system/
│
├── app.py                      # Main Flask application
├── detectin.py                 # Model initialization script
├── requirements.txt            # Python dependencies
├── .env                        # Environment variables (create this)
├── .gitignore                  # Git ignore rules
│
├── templates/                  # HTML templates
│   ├── index.html             # Home page
│   ├── register.html          # Employee registration
│   ├── login.html             # Employee check-in
│   ├── checkout.html          # Employee check-out
│   ├── admin_login.html       # Admin login
│   ├── admin_dashboard.html   # Admin dashboard
│   ├── attendance.html        # Attendance management
│   ├── view_users.html        # View all users
│   ├── edit_user.html         # Edit user details
│   └── delete_user.html       # Delete user
│
├── static/                     # Static files (CSS, JS, images)
│   ├── css/
│   ├── js/
│   └── images/
│
├── docs/                       # Documentation
│   ├── USER_MANUAL.md         # User manual
│   ├── API_DOCUMENTATION.md   # API reference
│   └── Technical-Report.pdf   # Technical report
│
└── README.md                   # This file
```

---

## API Endpoints

### Public Endpoints

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/` | GET | Home page |
| `/register` | GET, POST | Employee registration |
| `/login` | GET, POST | Employee check-in |
| `/checkout` | GET, POST | Employee check-out |
| `/video_feed` | GET | Live camera feed |

### Admin Endpoints

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/admin/login` | GET, POST | Admin authentication |
| `/admin/dashboard` | GET | Admin dashboard |
| `/admin/view_users` | GET | View all employees |
| `/admin/edit_user` | GET, POST | Edit employee data |
| `/admin/delete_user` | GET, POST | Delete single employee |
| `/admin/delete_all_users` | GET | Delete all employees |
| `/admin/logout` | GET | Admin logout |
| `/attendance` | GET, POST | Attendance reports |

---

## Database Schema

### Users Table

| Column | Type | Description |
|--------|------|-------------|
| id | INT | Primary key (auto-increment) |
| name | VARCHAR(100) | Employee name |
| embedding | LONGBLOB | Face embedding data |
| employee_id | VARCHAR(50) | Unique employee identifier |

### Employee Entries Table

| Column | Type | Description |
|--------|------|-------------|
| id | INT | Primary key (auto-increment) |
| employee_id | VARCHAR(50) | Foreign key to users |
| name | VARCHAR(100) | Employee name |
| entry_time | TIME | Check-in timestamp |
| exit_time | TIME | Check-out timestamp |
| entry_date | DATE | Date of entry |

---

## Security Features

### Authentication & Authorization
- **Password Hashing**: Bcrypt with salt rounds
- **Session Management**: Server-side session storage
- **Rate Limiting**: 5 login attempts per minute per IP
- **Lockout Mechanism**: 5-minute lockout after 3 failed attempts

### Face Recognition Security
- **Anti-Spoofing**: Multi-model validation (3 models)
- **Texture Analysis**: Laplacian variance check for print attacks
- **Age Verification**: Anomaly detection in age prediction
- **Similarity Threshold**: Cosine similarity > 0.75 required

### Data Protection
- **Encrypted Storage**: Face embeddings stored as binary
- **Environment Variables**: Sensitive data in `.env` file
- **Unique Constraints**: Prevents duplicate employee IDs
- **Session Timeout**: Automatic logout after 60 minutes inactivity

---

## Troubleshooting

### Common Issues

#### 1. Camera Not Detected

**Problem**: `Failed to capture image` error

**Solution**:
```python
# Try different camera indices
camera = cv2.VideoCapture(1)  # Try 1, 2, etc.
```

#### 2. MySQL Connection Error

**Problem**: `Error while connecting to MySQL`

**Solution**:
- Verify MySQL is running: `sudo service mysql status`
- Check credentials in `.env` file
- Test connection: `mysql -u root -p`

#### 3. Module Import Errors

**Problem**: `ModuleNotFoundError: No module named 'deepface'`

**Solution**:
```bash
pip install --upgrade deepface
pip install --upgrade tensorflow
```

#### 4. Face Not Recognized

**Problem**: Face not detected or matched

**Solution**:
- Ensure good lighting conditions
- Position face closer to camera
- Remove glasses/masks if possible
- Adjust similarity threshold in code

#### 5. Slow Performance

**Problem**: Recognition takes too long

**Solution**:
- Use GPU-enabled TensorFlow
- Reduce image resolution
- Optimize database queries
- Close other resource-intensive applications

---

## Contributing

We welcome contributions! Please follow these guidelines:

### How to Contribute

1. **Fork the repository**
2. **Create a feature branch**
   ```bash
   git checkout -b feature/AmazingFeature
   ```
3. **Commit your changes**
   ```bash
   git commit -m 'Add some AmazingFeature'
   ```
4. **Push to the branch**
   ```bash
   git push origin feature/AmazingFeature
   ```
5. **Open a Pull Request**

### Coding Standards
- Follow PEP 8 style guide for Python
- Add comments for complex logic
- Write descriptive commit messages
- Include docstrings for functions
- Test your changes thoroughly

### Reporting Issues
- Use the GitHub issue tracker
- Provide detailed description
- Include steps to reproduce
- Attach screenshots if applicable

---

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## Acknowledgments

- **DeepFace**: Face recognition library by Serengil
- **OpenCV**: Computer vision library
- **TensorFlow**: Deep learning framework
- **Flask**: Web framework by Pallets
- **InceptionResNetV2**: Pre-trained model by Google

---

## Contact

**Project Maintainer**: Your Name

- **Email**: your.email@example.com
- **GitHub**: [@yourusername](https://github.com/yourusername)
- **LinkedIn**: [Your LinkedIn](https://linkedin.com/in/yourprofile)

**Project Link**: [https://github.com/yourusername/face-recognition-entry-system](https://github.com/yourusername/face-recognition-entry-system)

---

## Changelog

### Version 1.0.0 (Current)
- Initial release
- Face recognition authentication
- Admin dashboard
- Attendance tracking
- Anti-spoofing detection
- MySQL database integration

### Planned Features
- Mobile app integration
- Cloud deployment
- Advanced analytics dashboard
- Multi-factor authentication
- Email notifications
- Export to CSV/PDF

---

**Made with ❤️ for efficient workforce management**
