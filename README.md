# gipfeli.io App Suite

This Docker configuration allows you to run the whole Gipfeli app locally with just one container.

## Prerequisites

* Docker must be installed on the system
* Ports 3000, 3001 & 3002 must be unused

## Configuration

After cloning the repository, the first step is to configure environment variables.

### gipfeli-api

Create a `.env` file in the `./gipfeli-api` directory. While you're free in configuring most of the variables as per the `.env.example` file, the following variables have to be set to meet the Docker requirements.

```
TYPEORM_HOST=database
TYPEORM_USERNAME=postgres
TYPEORM_PASSWORD=postgres
TYPEORM_DATABASE=postgres
TYPEORM_PORT=5432
CORS_ORIGIN=http://localhost:3001
```

As for the `GCS_BUCKET` and `GCS_SERVICE_ACCOUNT` variables, either provide a bucket on your own (with public access rights) or ask the gipfeli.io team for access to the dev bucket.

**Note regarding SendGrid:** If you set `ENVIRONMENT` to either *production* or *staging*, you also have to set the `SENDGRID_` variables because these environments use email notifications. Ask the gipfeli.io team for help.

**Note regarding Sentry:** If you set the `SENTRY_` variables, make sure you have a valid Sentry project. Ask the gipfeli.io team for help.

### gipfeli-frontend

Create a `.env` file in the `./gipfeli-frontend` directory. You're free in configuring most of the variables as per the `.env.example` file except for

* `REACT_APP_PUBLIC_BACKEND_API`: Set to `http://localhost:3000`
* `REACT_APP_STORAGE_BUCKET_BASE_URL`: Set to the base URL of your Google Storage bucket. Ask the gipfeli.io team for help.

**Note regarding Sentry:** If you set the `SENTRY_` variables, make sure you have a valid Sentry project. Ask the gipfeli.io team for help.

## Run

Once the variables are set, you can start the application by running `docker compose up` in the project's root directory. This will boot the application (which might take some time) and expose the following services:

* http://localhost:3000 -> Backend API
* http://localhost:3001 -> Frontend
* http://localhost:3002 -> Adminer database GUI

### Adding a user

To add a user, ssh into the container and use the normal `create-user` command and follow the prompts there:

```
# normally, the container should be gipfeli_api, but if you're running multiple instances, it might be gipfeli_api_1
docker exec -it gipfeli_api sh

# once logged in, make sure you're in the /app directory and run the following command
npm run create-user
```

**Note:** You might also use `docker exec -it gipfeli_api sh -c "npm run create-user"`, though the prompts do not work as nicely in the `sh` context (e.g. passwords not hidden, prompts duplicated, etc.).

## Commands

```
git submodule update --remote
```