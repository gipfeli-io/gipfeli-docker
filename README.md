# gipfeli.io App Suite

This Docker configuration allows you to run the whole Gipfeli app locally with just one container.

## Installation

Since we're using submodules, some additional commands are necessary to clone the repository correctly.

1. `git clone` this repository
2. `cd` into the newly cloned repository
3. Run `git submodule init`
4. Run `git submodule update`

This will fetch the submodules and their content, and you can go to the step of configuring the project.

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


## Commands

```
git submodule update --remote
```
