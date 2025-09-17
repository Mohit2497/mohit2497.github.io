---
title: "Beginner DE Bootcamp: Python"
date: 2025-09-17 10:00:00 +0000
categories: [Blog, Data-Engineering]
tags: [python, api]
excerpt: "Module 3.1 - Building a REST API Extractor with Python"
image:
    path: assets/img/blog/module4.jpeg 
    alt: Python Stocks App
---

I’m currently following **Zac Wilson’s Beginner Data Engineering Bootcamp** on [DataExpert.io](https://dataexpert.io), and this week I completed **Module 2.1**, which focuses on building a simple **REST API extractor** using Python.  

The goal of this mini-project was to build a script that connects to the [Polygon.io](https://polygon.io) API, pulls stock ticker data, and saves it into a clean CSV file. It’s a small but important milestone: reading data from an external API, handling multiple pages of results, and producing a structured dataset that’s ready for analysis.  

---

## 📌 What the Script Does

This project contains a single Python script, `script.py`, which:

- Connects to the Polygon.io API and requests a list of stock tickers  
- Follows the `"next"` link in the API response until all pages are fetched  
- Normalizes and writes the data into a CSV file (`tickers.csv`)  
- Ensures consistent columns for every row  
- Stops and shows an error message if the API returns any errors

You can find the source code for this project here:  
🔗 [stock-trading-python-app](https://github.com/Mohit2497/stock-trading-python-app)