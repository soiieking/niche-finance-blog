---
title: 'Rootprint: Open-source Log Search with Live Indexing on S3'
date: '2026-09-04 02:00:04+08:00'
draft: false
tags:
- selfhosted
- vps
- linux
- technology
summary: Rootprint lets you search logs stored on S3 in real time—no Elasticsearch,
  no Kafka, just a lightweight, open-source stack.
---

## Why Rootprint Matters

Logs pile up fast. Whether you're running a bunch of Docker containers, tinkering with a k3s cluster, or debugging a self-hosted Nextcloud instance, sooner or later, you're going to need to search through logs. 

Most open-source setups for serious log searching tend to spiral into complexity. Need something like Elasticsearch just to grep logs at scale? Ugh. Enter **Rootprint**, a lightweight alternative.

Rootprint is a log and trace search engine where the indexed data is stored **directly on S3**. No separate database, no Kafka or ZooKeeper nonsense. It’s built for simplicity, and its minimal overhead makes it perfect for smaller self-hosted setups—though, fair warning, it might not replace ELK if you’re operating at Netflix scale.

---

## What You'll Need

To follow this setup, you’ll need:

1. **A machine to run Rootprint**: Anything reasonably modern will do. Tested on Ubuntu 22.04, but any Linux distro should work.
2. **An S3 bucket**: AWS S3, MinIO, Wasabi—it doesn’t matter. As long as it speaks the S3 protocol.
3. **Docker** (or Podman, if that’s your vibe): Rootprint ships a simple container image.
4. A basic understanding of `jq` (optional) since we might filter some logs for testing.

Let’s get this running.

---

## Step 1: Provision Your S3 Bucket

If you don’t already have an S3-compatible bucket, now’s the time to create one. If you’re allergic to cloud pricing (aren’t we all?), you can roll your own using MinIO or play it cheap with Wasabi.

On AWS S3:
- Create a bucket, e.g., `my-log-bucket`.
- Set the region, e.g., `us-east-1`, and note your access credentials.
- Since Rootprint doesn’t pull Kafka-Scale-Data-Dog-Vibe security, make sure the bucket isn’t public.

Test your S3 bucket before moving on:
```bash
aws s3 ls s3://my-log-bucket --region us-east-1
```

If you see no errors, you’re good to go.

---

## Step 2: Deploy Rootprint

### Option 1: Docker Run Command

First, pull the latest Rootprint image:
```bash
docker pull ghcr.io/ampledata/rootprint:latest
```

Now run the container. Replace placeholder values with your S3 credentials and bucket details:

```bash
docker run -d \
  -p 8080:8080 \
  -e AWS_ACCESS_KEY_ID=your-access-key-id \
  -e AWS_SECRET_ACCESS_KEY=your-secret-access-key \
  -e ROOTPRINT_BUCKET=my-log-bucket \
  -e ROOTPRINT_REGION=us-east-1 \
  ghcr.io/ampledata/rootprint:latest
```

You should now have Rootprint running on [http://localhost:8080](http://localhost:8080).

### Option 2: Docker Compose for Persistent Config

Want something repeatable? Use Docker Compose:

```yaml
version: '3'

services:
  rootprint:
    image: ghcr.io/ampledata/rootprint:latest
    ports:
      - "8080:8080"
    environment:
      AWS_ACCESS_KEY_ID: your-access-key-id
      AWS_SECRET_ACCESS_KEY: your-secret-access-key
      ROOTPRINT_BUCKET: my-log-bucket
      ROOTPRINT_REGION: us-east-1
```

Bring it up:
```bash
docker compose up -d
```

Persisting logs? Bind-mount your files into the container under `/data`.

---

## Step 3: Send Some Logs!

Rootprint needs something to, y'know, print. A quick way to ship logs to S3 is to use the AWS CLI. Here's an example of piping `syslog` into a log file and pushing it:

```bash
tail -f /var/log/syslog | while read line; do
  echo $line >> app-logs-$(date +%Y-%m-%d).log
  aws s3 cp app-logs-$(date +%Y-%m-%d).log s3://my-log-bucket/
done
```

This is overkill for just testing. For a quick test, manually upload a file instead:
```bash
echo "Test log line: $(date)" > test-log.log
aws s3 cp test-log.log s3://my-log-bucket/
```

---

## Step 4: Query the Logs

Now visit [http://localhost:8080](http://localhost:8080). You should see a web UI to query your logs. Type something simple like `Test log` and confirm the uploaded line shows up.

Filters? Use query strings like:
- `app="my-service"`
- `level=ERROR`

Results are paginated for large datasets but watch your browser RAM usage. Somebody in the `r/selfhosted` thread joked about leaving Safari unresponsive after querying giant logs—it’s funny ’til it’s you.

---

## FAQ

### Does Rootprint Work with ARM?

Yes! While the GHCR image doesn’t explicitly tag ARM builds yet, it’s a Go app. Build it from source, and it’ll run on your Pi.

```bash
git clone https://github.com/ampledata/rootprint.git
cd rootprint
make
```

### How Much RAM Does This Use?

Depends. The container itself is a lightweight Go app, so expect <100 MB for basic use. If your logs are massive, though, S3 read speed becomes your bottleneck, not RAM.

### Why Use Rootprint Over ELK?

- **Small setups**: ELK’s `docker-compose` alone pulls GBs of RAM. Rootprint? A couple hundred MB is enough.
- **No Kafka**: You don’t need to teach yourself the Kafka ecosystem just to search some bloody logs.
- **S3 cost-control**: Store logs cheap, query only what you need.

That said, Rootprint might not scale for 500GB/day workloads. Judge your use case wisely.

---
