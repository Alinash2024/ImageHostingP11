# Image Hosting Service

A lightweight image hosting service with drag-and-drop upload functionality, PostgreSQL database backend, and automatic backups.

## Features

- Drag-and-drop image upload interface
- File type and size validation (JPEG, PNG, GIF up to 5MB)
- Unique URL generation for uploaded images
- Image gallery with pagination
- PostgreSQL database storage
- Automatic daily backups
- Responsive design

## Technology Stack

- **Frontend**: HTML5, CSS3, JavaScript
- **Backend**: Python (custom server)
- **Database**: PostgreSQL
- **Infrastructure**: Docker, Docker Compose
- **Other**: Nginx (reverse proxy)


## API Endpoints

### Image Upload
- **POST** `/api/upload/`
  - Headers: `Filename: [original_filename]`
  - Body: Raw image file
  - Response: 
    - Success: 200 with `Location` header containing the image URL
    - Failure: 4xx/5xx with error details

### Image Retrieval
- **GET** `/images/`
  - Returns: HTML page with paginated image gallery
- **GET** `/images/[filename]`
  - Returns: The actual image file

### Other Routes
- **GET** `/` - Main landing page
- **GET** `/upload` - File upload interface

### Database Schema
- CREATE TABLE images (
  - id SERIAL PRIMARY KEY,
  - filename VARCHAR(255),
  - original_name VARCHAR(255),
  - size INTEGER,
  - upload_time TIMESTAMP DEFAULT CURRENT_TIMESTAMP, 
  file_type VARCHAR(255)
);

### Setup Instructions
- **Prerequisites**
  - Docker and Docker Compose installed
  - PostgreSQL client (optional)
- **Installation**
  - Clone the repository: 
    - git clone https://github.com/Alinash2024/ImageHostingP11.git
    - cd ImageHostingP11
  - Start the services:
   - docker-compose up -d
  - Initialize the database (first run only):
   - docker exec -it imagehosting_app_1 python -c "from DBManager import DBManager; db = DBManager(); db.init_tables()"

The application will be available at http://localhost:8000

### Backup System
The project includes an automated backup solution with the following features:
- **Daily Backups**
  - Runs as a separate Docker service (auto_backup_compose.yml)
  - Creates timestamped SQL dumps of the database
  - Stores backups in /backups directory
- **Manual Backup**
  - docker exec -it imagehosting_db_1 pg_dump -U postgres image_db > backup_$(date +%Y-%m-%d).sql 

### Configuration
- **Key configuration options (in app/settings.py)**
  - SERVER_ADDRESS = ('0.0.0.0', 8000)  # Server bind address
  - STATIC_PATH = '/app/static/'         # Static files directory
  - IMAGES_PATH = '/app/images/'         # Image storage directory
  - ALLOWED_EXTENSIONS = ['.jpg', '.jpeg', '.png', '.gif']  # Permitted file types
  - MAX_FILE_SIZE = 5 * 1024 * 1024      # 5MB file size limit

### Development
- **To run in development mode**
  - docker-compose -f docker-compose.yml -f dev-compose.yml up