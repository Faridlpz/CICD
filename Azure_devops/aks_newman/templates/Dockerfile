# Use a Node base image
FROM node:latest

# Create a directory to hold the collection and data files
WORKDIR /app

# Install Newman globally
RUN npm install -g newman

# Set the workdir as a volume to allow mounting of files at runtime
VOLUME ["/app"]

# By default, run Newman with no parameters
CMD ["newman"]