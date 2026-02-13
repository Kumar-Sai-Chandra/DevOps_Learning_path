# Use the official MySQL image as the base image
FROM mysql:8.0.33
# label the image with metadata about the project, author, and description
LABEL "Project"="vprofile-project" "Author"="Kumar" "Description"="MySQL database for vprofile-project"
# Set environment variables for MySQL root password and database name
ENV "MYSQL_ROOT_PASSWORD"="vprodbpass"
ENV "MYSQL_DATABASE"="accounts"

# Add the SQL backup file to the MySQL initialization directory so that it will be executed when the container starts
ADD db_backup.sql /docker-entrypoint-initdb.d/db_backup.sql

