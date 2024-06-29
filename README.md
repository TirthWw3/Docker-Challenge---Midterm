
# Docker Challenge Project

## Docker Challenge 1 - Simple Web Server for Static Web Pages

### Project Setup

Created a directory named `docker-challenge-1`. Added an `index.html` file with name and student ID.

#### index.html Content

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Docker Challenge 1</title>
</head>
<body>
    <h1>Name: Tirth</h1>
    <h2>Student ID: 000900022</h2>
</body>
</html>
```

#### Dockerfile Content

```dockerfile
# Use the official Nginx image from the Docker Hub
FROM nginx:alpine

# Copy the static website files to the Nginx HTML directory
COPY . /usr/share/nginx/html

# Expose port 80
EXPOSE 80
```

### Building the Docker Image

```sh
docker build -t my-nginx-server .
```

### Running the Docker Container

```sh
docker run -d -p 8082:80 simple-web-server
```

### Verifying the Setup

Access `http://localhost:8082/` in the browser to verify the web page is being served correctly.

## Docker Challenge 2 – Creating a Dynamic Application

### Steps Followed

1. **Set Up the Project Directory**
    ```sh
    mkdir docker-dynamic-app
    cd docker-dynamic-app
    ```

2. **Initialize a new Node.js application**
    ```sh
    npm init -y
    ```

3. **Install Express.js**
    ```sh
    npm install express
    ```

4. **Create the `server.js` file**
    ```javascript
    const express = require('express');
    const app = express();
    const PORT = 3000;

    const books = [
        { id: 1, title: 'Book 1', author: 'Author 1' },
        { id: 2, title: 'Book 2', author: 'Author 2' }
    ];

    app.get('/api/books', (req, res) => {
        res.json(books);
    });

    app.get('/api/books/:id', (req, res) => {
        const book = books.find(b => b.id == req.params.id);
        if (book) {
            res.json(book);
        } else {
            res.status(404).send('Book not found');
        }
    });

    app.listen(PORT, () => {
        console.log(`Server is running on http://localhost:${PORT}`);
    });
    ```

5. **Create a `Dockerfile` for the server**
    ```dockerfile
    # Use the official Node.js image
    FROM node:14

    # Create app directory
    WORKDIR /usr/src/app

    # Install app dependencies
    COPY package*.json ./
    RUN npm install

    # Bundle app source
    COPY . .

    # Bind to port 3000
    EXPOSE 3000

    # Run the app
    CMD ["node", "server.js"]
    ```

### Set Up Nginx

1. **Create a directory for Nginx configuration**
    ```sh
    mkdir nginx
    cd nginx
    ```

2. **Create the Nginx configuration file**
    ```nginx
    server {
        listen 8080;

        location / {
            proxy_pass http://server:3000;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }
    ```

3. **Create a `Dockerfile` for Nginx**
    ```dockerfile
    FROM nginx:alpine
    COPY nginx.conf /etc/nginx/conf.d/default.conf
    ```

### Create Docker Compose File

```yaml
version: '3.7'

services:
  server:
    build: .
    ports:
      - "3000:3000"

  nginx:
    build: ./nginx
    ports:
      - "8080:8080"
    depends_on:
      - server
```

### Build and Run the Application

1. **Build and start the Docker Compose application**
    ```sh
    docker-compose up --build
    ```

2. **Verify the application is running**
    - Open a browser and go to `http://localhost:8080/api/books`
      - You should see a JSON message with all books.
    - Go to `http://localhost:8080/api/books/1`
      - You should see a JSON message with just one book.

## References

IEEE format:

- Author, "Title of Document," unpublished. Available: https://www.example.com.
