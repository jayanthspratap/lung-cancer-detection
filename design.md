Lung.ai: An Automated Approach to Lung Cancer Prognosis Prediction Using OpenCV and Flask

Below, we detail the three main components of our project: image processing, data storage, and the web application.

IMAGE PROCESSING

We utilized OpenCV for image segmentation, specifically to perform the operations that isolate the tumor from the PET scan. The general pipeline for image processing is:

1) The raw PET scan image is stored in the project directory, and is read using the imread() method
2) The image is binarized using a manual threshold
3) Noise reduction is performed via a closing operation with an elliptical kernel
4) Contours in the PET scan are detected using the findContours() method
5) The area of each contour is found using the contourArea() method and the maximum area is selected, as a representation of the tumor cross-section
6) The pixels corresponding to the isolated tumor in the PET scan are outlined using the drawContours() method
7) The annotated image is saved to the project directory using the imwrite() method

DATA STORAGE

We use SQL to create a database data.db that stores the data for our project. We decided to use SQLite because it is commonly used as a relational database management system for local storage in web applications. The data.db file is a database file that stores three different tables: doctors, patients, and scans. 

1) The doctors table contains a primary id, a username, and a password. This stores the login information from when doctors register to use our web application. 
2) The patients table contains a primary id, a name, a date of birth, and a doctor id, for when doctors add patients to the system. 
3) Lastly, the scans table contains a primary id, a patient id, a raw scan, an annotated scan, an area, and a date.

WEB APPLICATION

We utilized Flask, Python, and Jinja to build our web server, and HTML and CSS to design each webpage. 

Starting off with our registration page, we create a Flask route in app.py to facilitate the creation of our registration page. We use Python to ensure the username, password, and password confirmation are all submitted. We then use a SQL query to query the database for usernames to ensure that the username entered doesn’t already exist. We then generate a password hash to ensure the security of our registration information. We then execute another SQL query and render our HTML templates. Following registration, the user is brought to a login page. For the login page, we again created a Flask route in app.py. We use Python to ensure that a username and password is submitted and use SQL queries to check that the username exists and the password is correct. Our code then renders the appropriate HTML templates.  	

Our add and delete functions are created in a similar manner. We create Flask routes in app.py and use python to ensure that the proper and relevant information is entered. SQL queries are then used to interact with our database and extract or remove the relevant information. Our code then renders the appropriate HTML templates.

We will now discuss how scans are uploaded to and retrieved from the database. Note that images in the database are stored in the BLOB (Binary Large Object) format, meaning images must be interconverted between formats when storing and retrieving data. In OpenCV, images are stored as uint8 arrays with numpy. These uint8 arrays must be converted to bytes when storing images in the database, and this is done with the imencode() and .tobytes() methods used in conjunction. Conversely, when retrieving data from the database and displaying it in the web application, the data must be read from the BLOB format to a uint8 array, which is done using the imdecode() and frombuffer() methods.

To upload a scan, the user selects a file from their computer, which is then saved to the project directory as the raw PET scan. This file is read as a BLOB and inserted into the database with a SQL INSERT statement, in conjunction with the current date retrieved using the datetime library in Python. The image is also saved to the project directory and passed to the OpenCV pipeline, which generates the annotated PET scan and the tumor cross-sectional area, and returns this data to be inserted into the database.

Subsequently, when looking up PET scans that have already been uploaded, the user is prompted to select a patient and a date. Once these have been selected, a SQL query is executed and the corresponding row in the scans table (within the data.db database) is returned. The textual data (name, date, area) is passed to the HTML template as-is, but the images must first be converted from bytes and stored in the project directory using the imdecode() and frombuffer() methods, and the HTML template reads the images from this project directory to render the final page.