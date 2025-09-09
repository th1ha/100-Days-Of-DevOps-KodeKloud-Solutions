# Install and Configure PostgreSQL

```bash
sudo -u postgres psql

CREATE DATABASE kodekloud_db8;

CREATE USER kodekloud_roy WITH ENCRYPTED PASSWORD 'YchZHRcLkL';

GRANT ALL ON DATABASE kodekloud_db8 TO kodekloud_roy;

\du

```