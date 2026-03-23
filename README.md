#  Document Counter Application

[![Python Version](https://img.shields.io/badge/python-3.8+-blue.svg)](https://python.org)
[![Flask Version](https://img.shields.io/badge/flask-2.3.2-green.svg)](https://flask.palletsprojects.com/)
[![License](https://img.shields.io/badge/license-MIT-orange.svg)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)

An intelligent, automatic document counting application that processes multiple document formats and provides comprehensive statistics including page counts, word counts, and character counts with OCR support for images.

##  Features

###  Core Capabilities
- **Multi-format Support**: Process PDF, DOCX, XLSX, images (JPG, PNG, TIFF), and text files
- **Automatic Counting**: Instant page, word, and character counting
- **OCR Integration**: Extract text from images using Tesseract OCR
- **Real-time Processing**: Immediate results after upload
- **Duplicate Detection**: MD5 hashing to prevent duplicate processing

###  User Interface
- **Drag & Drop Upload**: Easy file upload interface
- **Responsive Design**: Works on desktop, tablet, and mobile
- **Statistics Dashboard**: Real-time analytics display
- **Document Table**: Sortable and searchable document list
- **Export Functionality**: One-click CSV export

### Technical Features
- **SQLite Database**: Persistent document storage
- **RESTful API**: Clean API endpoints for integration
- **Batch Processing**: Handle multiple files simultaneously
- **Error Handling**: Graceful failure management
- **Logging**: Comprehensive logging for debugging

##  Quick Start

### Prerequisites

- Python 3.8 or higher
- Tesseract OCR (for image text extraction)
- pip package manager

### Installation

1. **Clone the repository**
```bash
git clone https://github.com/yourusername/document-counter-app.git
cd document-counter-app
```

2. **Install Tesseract OCR**

   **Windows:**
   ```bash
   # Download from: https://github.com/UB-Mannheim/tesseract/wiki
   # Add to PATH: C:\Program Files\Tesseract-OCR\
   ```

   **Linux (Ubuntu/Debian):**
   ```bash
   sudo apt-get update
   sudo apt-get install tesseract-ocr
   ```

   **macOS:**
   ```bash
   brew install tesseract
   ```

3. **Install Python dependencies**
```bash
pip install -r requirements.txt
```

4. **Run the application**
```bash
python document_counter_app.py
```

##  Usage Guide

### Uploading Documents

1. **Drag and Drop**: Simply drag files into the upload area
2. **Click to Upload**: Click the upload area and select files
3. **Multiple Files**: Select multiple files for batch processing

### Viewing Statistics

The dashboard automatically updates with:
- Total documents count
- Total pages across all documents
- Total word count
- Total character count
- Processed vs. failed documents

### Managing Documents

- **Document Table**: View all uploaded documents with their metrics
- **Real-time Updates**: Table refreshes automatically every 30 seconds
- **Status Indicators**: Green for success, red for failures

### Exporting Data

Click the **Export to CSV** button to download all document data with:
- Filename
- File type
- Page count
- Word count
- Character count
- Upload date
- Processing status

##  Configuration

### File Size Limits
Modify in `document_counter_app.py`:
```python
app.config['MAX_CONTENT_LENGTH'] = 50 * 1024 * 1024  # 50MB
```

### Tesseract Path (Windows)
If OCR isn't working, set the Tesseract path:
```python
pytesseract.pytesseract.tesseract_cmd = r'C:\Program Files\Tesseract-OCR\tesseract.exe'
```

### Database Location
The SQLite database is created as `documents.db` in the application directory.

##  Project Structure

```
document-counter-app/
├── document_counter_app.py   # Main application
├── requirements.txt           # Python dependencies
├── README.md                  # Documentation
├── templates/
│   └── index.html            # Web interface
├── uploads/                  # Uploaded files storage
├── documents.db              # SQLite database
└── exports/                  # Exported CSV files
```

##  API Endpoints

| Endpoint | Method | Description | Response |
|----------|--------|-------------|----------|
| `/` | GET | Main web interface | HTML |
| `/api/upload` | POST | Upload and process documents | JSON |
| `/api/documents` | GET | List all documents | JSON |
| `/api/statistics` | GET | Get overall statistics | JSON |
| `/api/export` | GET | Export data as CSV | CSV file |

### API Usage Examples

**Upload Files:**
```bash
curl -X POST -F "files=@document.pdf" -F "files=@image.jpg" http://localhost:5000/api/upload
```

**Get Statistics:**
```bash
curl http://localhost:5000/api/statistics
```

**Export Data:**
```bash
curl http://localhost:5000/api/export --output documents.csv
```

## 🛠️ Technology Stack

### Backend
- **Flask**: Web framework
- **SQLite3**: Database
- **PyPDF2**: PDF processing
- **python-docx**: Word document processing
- **openpyxl**: Excel processing
- **Pillow**: Image processing
- **pytesseract**: OCR engine
- **pandas**: Data export

### Frontend
- **HTML5**: Structure
- **CSS3**: Styling with gradients and animations
- **JavaScript**: Dynamic content and API calls
- **Fetch API**: Asynchronous requests

##  Database Schema

```sql
-- Documents table
CREATE TABLE documents (
    id INTEGER PRIMARY KEY,
    filename TEXT,
    file_path TEXT,
    file_hash TEXT UNIQUE,
    file_size INTEGER,
    file_type TEXT,
    page_count INTEGER,
    word_count INTEGER,
    character_count INTEGER,
    upload_date TIMESTAMP,
    processed_date TIMESTAMP,
    status TEXT,
    metadata TEXT
);

-- Batches table
CREATE TABLE batches (
    id INTEGER PRIMARY KEY,
    batch_name TEXT,
    created_date TIMESTAMP,
    total_documents INTEGER,
    total_pages INTEGER,
    total_words INTEGER
);
```

##  Troubleshooting

### Common Issues

**Issue: OCR not working**
- **Solution**: Install Tesseract OCR and verify path
- **Check**: Run `tesseract --version` in terminal

**Issue: Database locked**
- **Solution**: Close other connections or restart application
- **Fix**: Delete `documents.db` and restart (will recreate)

**Issue: Memory errors with large files**
- **Solution**: Increase memory limit or process smaller batches
- **Workaround**: Use the `MAX_CONTENT_LENGTH` configuration

**Issue: Unsupported file type**
- **Solution**: Add new file type to the `get_counter` function

### Logs
Check application logs for detailed error information:
```python
# Logs are printed to console by default
# You can redirect to a file:
logging.basicConfig(filename='app.log', level=logging.INFO)
```

##  Performance Optimization

### For Large Deployments

1. **Use a production WSGI server**
```bash
pip install gunicorn
gunicorn -w 4 -b 0.0.0.0:5000 document_counter_app:app
```

2. **Add caching for statistics**
```python
from functools import lru_cache

@lru_cache(maxsize=128)
def get_cached_statistics():
    return DocumentDB.get_statistics()
```

3. **Implement async processing**
```python
import asyncio
from concurrent.futures import ThreadPoolExecutor

executor = ThreadPoolExecutor(max_workers=4)
```

##  Security Considerations

- **File Validation**: Only allowed extensions are processed
- **Path Traversal**: Filenames are sanitized
- **Size Limits**: Maximum file size configurable
- **SQL Injection**: Parameterized queries used
- **XSS Protection**: HTML escaping in templates

##  Contributing

Contributions are welcome! Please follow these steps:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit changes (`git commit -m 'Add AmazingFeature'`)
4. Push to branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

### Development Setup

```bash
# Install development dependencies
pip install -r requirements-dev.txt

# Run tests
pytest tests/

# Check code style
flake8 document_counter_app.py
```

##  License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

##  Acknowledgments

- [Tesseract OCR](https://github.com/tesseract-ocr/tesseract) for image text extraction
- [Flask](https://flask.palletsprojects.com/) for the web framework
- [PyPDF2](https://pypi.org/project/PyPDF2/) for PDF processing
- All other open-source libraries used in this project


## Author

Om Gedam

GitHub: [https://github.com/itsomg134](https://github.com/itsomg134)

Email: [omgedam123098@gmail.com](mailto:omgedam123098@gmail.com)

Twitter (X): [https://twitter.com/omgedam](https://twitter.com/omgedam)

LinkedIn: [https://linkedin.com/in/omgedam](https://linkedin.com/in/omgedam)

Portfolio: [https://ogworks.lovable.app](https://ogworks.lovable.app)
