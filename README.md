# gipfeli.io App Suite

This Docker configuration allows you to run the whole Gipfeli app locally with just one container. It pulls in the main branch.

## Installation

Since we're using submodules, some additional commands are necessary to clone the repository correctly.

1. `git clone` this repository
2. `cd` into the newly cloned repository
3. Run `git submodule init`
4. Run `git submodule update`

This will fetch the submodules and their content, and you can go to the step of configuring the project.

## Configuration

After cloning the repository, the first step is to configure environment variables.


> **_NOTE:_** If you copy the .env.example file to create a new .env file please be aware that comments on the same
> line as variables (e.g. `SOME_VAR= #this is the comment`) must be removed, otherwise there will be build errors.


### gipfeli-api

Create a `.env` file in the `./gipfeli-api` directory. While you're free in configuring most of the variables as per the `.env.example` file, the following variables have to be set to meet the Docker requirements.

```
TYPEORM_HOST=database
TYPEORM_USERNAME=postgres
TYPEORM_PASSWORD=postgres
TYPEORM_DATABASE=postgres
TYPEORM_PORT=5432
CORS_ORIGIN=http://localhost:3001
JWT_SECRET=somesecretvalueofyourchoice
```

As for the `GCS_BUCKET` and `GCS_SERVICE_ACCOUNT` variables, either provide a bucket on your own (with public access rights) or ask the gipfeli.io team for access to the dev bucket.

#### SendGrid: 
If you set `ENVIRONMENT` to either *production* or *staging*, you also have to set the `SENDGRID_` variables because these environments use email notifications. Ask the gipfeli.io team for help.

#### Sentry:
If you set the `SENTRY_` variables, make sure you have a valid Sentry project. Ask the gipfeli.io team for help.

#### ENVIRONMENT 
If you don't set the environment it will automatically be set to `localhost`. 
This means that when you try to create a user using the sign up form you have to fetch the activation link from the docker console, as the local notification provider does not send out emails, but prints to the console. You can also use the wizard or adjust the status in the database directly (see below under `Creating a user`).

### gipfeli-frontend

Create a `.env` file in the root directory (`./.env`, alongside e.g. the `docker-compose.yml` file). You're free in configuring most of the variables as per the `./gipfeli-frontend/.env.example` file except for

* `REACT_APP_PUBLIC_BACKEND_API`: Set to `http://localhost:3000`
* `REACT_APP_STORAGE_BUCKET_BASE_URL`: Set to the base URL of your Google Storage bucket. Ask the gipfeli.io team for help.

**Important:** Please check twice that you're adding the `.env` file in the root directory. The reason for this is that these environment variables are needed at buildtime and passed as build args in our docker compose configuration. As such, they need to be present in docker-compose and that only works if  you add a `.env` file at the root. This is in contrast to the backend, which needs the file at runtime and requires it in its directory.

#### Sentry:
If you set the `SENTRY_` variables, make sure you have a valid Sentry project. Ask the gipfeli.io team for help.

## Run

Once the variables are set, you can start the application by running `docker compose up` in the project's root directory. This will boot the application (which might take some time) and expose the following services:

* http://localhost:3000 -> Backend API
* http://localhost:3001 -> Frontend
* http://localhost:3002 -> Adminer database GUI

### Creating a user
**Create user using wizard (preferred way!)**

The docker containers need to be running for this!
1. Open Terminal
2. Enter `docker ps`
3. Find backend docker container id
4. Exec into the container e.g. `docker exec -it CONTAINER_ID sh` (replace CONTAINER_ID!)
5. Execute `npm run create-user`

**Activate user created by sign up form**

The docker containers need to be running for this!
1. Open terminal
2. Enter `docker ps`
3. Find postgres docker container id
4. Exec into the container e.g. `docker exec -it CONTAINER_ID bash` (replace CONTAINER_ID!)
5. Execute `psql -U postgres -W`
6. Enter the password for the database (see value of TYPEORM_DATABASE above)
7. Execute `UPDATE public.user SET "isActive"=true;`

### Possible issues
We are using IndexedDB to store our data for offline use. If you already accessed the frontend on localhost:3001 using another approach than 
the docker containers be aware that there can be an issue with the database versions used. 

If you get a `DatabaseClosedError` with message `VersionError The requested version (xx) is less than version...` please use our clean up functionality.
You can find it here: http://localhost:3001/profile/reset

## How to update submodules

Since we're using submodules, we have to update this repository manually when we want to point at a new release.

```
git submodule init
git submodule update --remote
```

And then add all the changes and commit and push.
