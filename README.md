# delivery_process
Repo for all the integrations built into the delivery process

## Security

### Handling Credentials
**Never commit service account keys or other sensitive credentials to the repository.**

To provide credentials for local development or in production:
1. Use environment variables (e.g., `GOOGLE_APPLICATION_CREDENTIALS`).
2. Use Google Cloud Secret Manager to securely store and retrieve secrets.
3. For local development, use the [Google Cloud CLI](https://cloud.google.com/sdk/docs/install) to authenticate:
   ```bash
   gcloud auth application-default login
   ```

### Rotating Compromised Keys
If a private key has been committed to the repository:
1. **Immediately revoke the key** in the Google Cloud Console.
2. Delete the key file from the repository.
3. Update the `.gitignore` file to prevent future accidental commits of sensitive files.
