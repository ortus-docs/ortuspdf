---
description: Issues? We all have them, learn how to cope with them
---

# Debugging

Here's a general list of troubleshooting steps if you're getting errors. The log files are VERY important since Lucee's error doesn't always contain the information you need. You must crack open your Lucee logs to get the actual error out.

* Look in the `out` and `err` logs for information. These are usually found in the servlet container's log directory. For instance, if you're using Tomcat, look in Tomcat's `log` directory. Messages are often logged there that never make it up to the CFML engine.
* Always scroll to the bottom of Java stack traces to look for a **caused by** section, which is the original error.
