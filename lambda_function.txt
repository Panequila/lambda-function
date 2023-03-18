import pymysql
import boto3 # The AWS SDK for python
import json

# Set the environment variables for the database credentials
DB_HOST = "db.cazwphmyjzvo.eu-north-1.rds.amazonaws.com"
DB_USER = "admin"
DB_PASSWORD = "admin123"
DB_NAME = "records"

#app = Flask(__name__)
#app.app_context().push()

# Connecting to the database
conn = pymysql.connect(host=DB_HOST, user=DB_USER, password=DB_PASSWORD, database=DB_NAME)

# Defining a function to handle GET requests
def get_data():
    
    # Execute a query to get the data from the database
    #The cursor is used to traverse the records from the result returned by the database. it's provided by the pymysql library.
    with conn.cursor() as cur:
        cur.execute("SELECT * FROM albums")
        data = cur.fetchall()

    # The API response
    # The json.dumps() function converts python object into a JSON formatted string.
    api_response = {
        'statusCode': 200,
        'headers': {
            'Content-Type': 'application/json'
        },
        'body': json.dumps(data)
    }

    return api_response
    
    
# Define a function to handle POST requests
def add_data():
    
    # Extracting the data from the request
    #name = data['Ramez']
    #age = data['23']

    # Inserting the data into the database
    with conn.cursor() as cur:
        cur.execute("INSERT INTO albums(id,name,release_year,band_id) VALUES (%s,%s,%s,%s)", (31, 'Ramezs Album', 2000, 1 ))
        conn.commit()

    # The API response
    api_response = {
        'statusCode': 200,
        'headers': {
            'Content-Type': 'application/json'
        },
        'body': json.dumps({'message': 'Data added successfully'})
    }

    return api_response


# Lambda function handler
def lambda_handler(event, context):
    
    # Extract the HTTP method from the event.
    # I couldn't get this to work so I statically assigned the request to "GET".
    http_method = event.get('httpMethod')
    print(http_method)
    http_method = "GET"
    print(http_method)


    # Route the request to the appropriate function
    # The server response with a status code (200 is successful, 400 isn't)
    if http_method == 'GET':
        response = get_data()
    elif http_method == 'POST':
        response = add_data()
    else:
        response = {
            'statusCode': 400,
            'headers': {
                'Content-Type': 'application/json'
            },
            'body': http_method
        }

    return response
