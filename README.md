This project is designed to process NLP (Natural Language Processing) tasks, including model training and predictions, using Python and various machine learning libraries. The application is containerized using Docker, and the scripts are scheduled to run automatically using cron jobs.

### Scripts Included

1. **helpers.py**: This script contains various helper functions that are used across different NLP and processing tasks. It is utilized by both the training and prediction scripts to assist in pre-processing and model-related tasks.

2. **nltktraining.py**: Responsible for training an NLP model using the NLTK library along with other dependencies. This script can be scheduled to run periodically via cron to ensure the model is updated regularly.

3. **nltkprediction.py**: Uses the trained NLP model to perform predictions on new datasets. Like the training script, this too is scheduled as a cron job to run automatically at specified intervals.

### Configuration Files

#### cronfile
The cronfile is used to schedule the periodic execution of the training and prediction scripts. It contains the following cron jobs:

- **Training Job**: Scheduled to run every Tuesday at 12:38 PM.
  ```
  38 12 * * 2 /usr/local/bin/python /usr/app/src/nltktraining.py >> /var/log/cron.log 2>&1
  ```

- **Prediction Job**: Scheduled to run every 5 minutes to predict outcomes using the trained model.
  ```
  */5 * * * * /usr/local/bin/python /usr/app/src/nltkprediction.py >> /var/log/cron.log 2>&1
  ```

### Dockerfile

The `Dockerfile` sets up the environment necessary for running the NLP tasks. It installs Python, the required libraries, and configures cron to execute the tasks defined in the cronfile.

Key commands in the `Dockerfile`:

- **Base Image**: Uses the latest version of Python.
- **Work Directory**: Sets `/usr/app/src` as the working directory in the container.
- **Library Installation**: Installs necessary libraries such as `pandas`, `tensorflow`, `scikit-learn`, `pymongo`, and `nltk`.
- **Cron Setup**: Copies the `cronfile` into `/etc/cron.d/` and configures cron to execute it.
- **Permission Settings**: Ensures appropriate permissions are set for the scripts and cronfile.
- **Cron Log**: Creates a log file for cron output at `/var/log/cron.log`.

### How to Use

#### Prerequisites:
- Docker must be installed on your system.
- Ensure that the required Python libraries (`pandas`, `tensorflow`, `scikit-learn`, `pymongo`, `nltk`) are installed, or use Docker to manage dependencies automatically.

#### Steps to Build and Run:

1. **Build the Docker Image**:
   Run the following command in the directory containing the `Dockerfile`:
   ```
   docker build -t ticket-processor .
   ```

2. **Run the Docker Container**:
   Once the image is built, you can run the container with the following command:
   ```
   docker run -d ticket-processor
   ```

This will start the container and enable cron to execute the training and prediction scripts based on the schedule defined in the `cronfile`.

#### Logs:
The output of the cron jobs is logged in `/var/log/cron.log` inside the container. You can inspect the logs by connecting to the container or using Docker log commands:
```
docker logs <container_id>
```
