# Add secrets

{% embed url="https://docs.google.com/spreadsheets/d/1OceOFYtdY4bFkgbIWZIeEiRs8xJnKTS4HrffI1TTuyI/edit?usp=sharing" %}

1. add to spreadsheet
2. add to github actions
3. update github actions to pull the secret to serve. Do this for the dev, test and prod actions. eg. .github/workflows/Dev Server Deploy Latest Main.yml and .github/workflows/Testing Server Deploy Latest.yml and .github/workflows/Prod Server Deploy Latest.yml
   1. add another line with the new secret like this.

<figure><img src=".gitbook/assets/Screenshot 2025-03-24 at 10.47.08 AM.png" alt=""><figcaption></figcaption></figure>

4. update all 3 docker compose files to pass the secret from the server env into the container env.\
   ![](<.gitbook/assets/Screenshot 2025-03-24 at 10.48.16 AM.png>)

for example, for a backend service docker compose.&#x20;

<figure><img src=".gitbook/assets/Screenshot 2025-03-24 at 10.49.24 AM.png" alt=""><figcaption></figcaption></figure>

4. if you want the secret to go into the django backend, take the secret from the container env into the django settings.py

<figure><img src=".gitbook/assets/Screenshot 2025-03-24 at 10.51.06 AM.png" alt=""><figcaption></figcaption></figure>
