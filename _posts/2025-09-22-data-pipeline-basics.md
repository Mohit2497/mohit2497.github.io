---
title: "Beginner DE Bootcamp: Data Pipeline Basics"
date: 2025-09-22 10:00:00 +0000
categories: [Blog, Data-Engineering]
tags: [python]
excerpt: "Module 4 - Data Pipeline Scheduler"
image:
    path: /assets/img/blog/module5.jpeg 
    alt: Pipeline Scheduler
---

As part of **Zac Wilson’s Beginner Data Engineering Bootcamp** on [DataExpert.io](https://dataexpert.io), I just finished the **Data Pipeline Basics** module. This lesson introduced me to the fundamentals of pipelines, why they matter, and how scheduling plays a role in automating data workflows.

---

## What is a Data Pipeline?

A **data pipeline** is a system that allows data to move from one place to another, often transforming it along the way. The end goal is to turn raw data into **insight or knowledge** that supports better business decisions.  

Pipelines are not only critical for analytics — they are also important in **AI systems**, where this concept is often referred to as **context engineering**: getting all the right data into one place so that models can use it effectively.

---

## Types of Data Pipelines

I learned that pipelines can be classified into three main types:

1. **Scheduled Pipelines**  
   - Run at fixed intervals (e.g., hourly, daily).  
   - Example: generating a daily sales report.

2. **Real-time Pipelines**  
   - Process events as soon as they happen.  
   - Example: **fraud detection systems**, which must react instantly to new transactions.

3. **On-demand Pipelines**  
   - Triggered only when needed.  
   - Example: processing a dataset uploaded by a user.

---

## Scheduling with Cron and Other Tools

Scheduling is a fundamental part of pipelines. One of the most common tools is **Cron**, which allows you to schedule tasks to run automatically at specific times or intervals. For example, you can run a job every 5 minutes or once a day at midnight.  

Beyond Cron, there are full-featured **orchestration frameworks** that help manage pipelines:  
- **Apache Airflow**  
- **Dagster**  
- **Prefect**  
- **Mage**

These tools provide features like retries, monitoring, and dependency management, making it easier to keep pipelines reliable at scale.

---

## My Hands-On Project: Adding Scheduling to My Extractor

In the previous module, I built a Python script (`script.py`) that connects to the **Polygon.io API**, fetches stock tickers, and saves them into a CSV file.  

In this module, I took the project a step further by introducing **scheduling**. I created a new script, `scheduler.py`, which imports and runs the `run_stock_job` function from `script.py` at regular intervals.  

Here’s what `scheduler.py` does:

- Defines a simple job that prints the time when it runs.  
- Uses the `schedule` Python library to run tasks every minute.  
- Calls the `run_stock_job` function (from `script.py`) on a schedule, so the CSV file gets updated automatically.  

Code snippet from `scheduler.py`:

```python
import schedule
import time
from script import run_stock_job
from datetime import datetime

def basic_job():
    print("Job started at", datetime.now())

# Run every minute
schedule.every().minute.do(basic_job)
schedule.every().minute.do(run_stock_job)

while True:
    schedule.run_pending()
    time.sleep(1)
```

I also experimented with Windows Task Scheduler to run `scheduler.py` automatically. This way, even if I forget to run the script manually, the operating system takes care of it for me.

## Takeaways

This module helped me understand that pipelines are about much more than moving data. They are about automation, reliability, and timing.

By connecting my extractor script to a scheduler, I experienced first-hand how a simple pipeline can evolve from a one-off script into a repeatable process. This is the foundation of real-world data engineering.

Thanks again to Zac Wilson. The Data Pipeline Basics module made the concepts much more tangible, and I’m excited to keep building on this.