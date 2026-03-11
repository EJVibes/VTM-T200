# MBT_ETM-T200
The code for MBT ETM T200 code base
This is the tickter style ETM

## How to Run

### Step 1: Clone the Repository
First, clone the repository to your local machine:

```bash
git clone https://github.com/Kai-codin/MBT-ETM-T200.git
```

### Step 2: Navigate to the Project Directory
Change to the project directory:

```bash
cd MBT-ETM-T200
```

### Step 3: Running Locally
To run the application locally, use the following command:

```bash
npm run serve
```

### Step 4: Running in Production
To build and run the application in a production environment, follow these steps:

1. Build the project:

    ```bash
    npm run build
    ```

2. Start the production server:

    ```bash
    PORT=3000 npm run start
    ```

### Railway Deployment

Railway can use the built-in scripts in [package.json](package.json):

```bash
npm install
npm run build
npm run start
```

The `start` script serves the compiled `dist` directory on Railway's `PORT` environment variable.
