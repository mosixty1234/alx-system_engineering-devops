# Postmortem

![Flogging a dead horse](post-mortem-meetings.jpg)

Issue Summary
Following the recent release of a new feature on our Ruby on Rails site, we encountered a sudden influx of user complaints. Within just 5 minutes of deploying the feature update, we received a flood of emails from users reporting difficulties signing in or signing up on our platform. This was quite unexpected since the feature had been working flawlessly on our development machines and had functioned properly prior to deployment. We received approximately 127 such emails, which was a significant concern for us as user retention is paramount. We promptly decided to investigate the issue.

Upon closer examination, we discovered that our site failed to start up after cloning the repository from GitBug and following the installation instructions outlined in the README. It didn't take long to identify the root cause: we had neglected to update the project's dependencies correctly. This issue persisted from 9:55 AM GMT+2 to 11:20 AM GMT+2.

Timeline
05-02-2022 9:55 AM GMT+2 - A user reported difficulties signing in.
05-02-2022 10:20 AM GMT+2 - Winus, one of our backend developers, encountered the same issue.
05-02-2022 10:35 AM GMT+2 - We began inspecting the controllers and views for inconsistencies.
05-02-2022 10:40 AM GMT+2 - Suspecting an issue with the bcrypt gem (a dependency of our site), we investigated as the error message indicated a problem with an invalid hash.
05-02-2022 10:42 AM GMT+2 - We checked if the views correctly bound the form fields to the model fields, but it turned out to be a false lead.
05-02-2022 10:45 AM GMT+2 - We initially believed our controllers might be generating a different hash for the site's admin's valid password.
05-02-2022 10:50 AM GMT+2 - Winus speculated that the issue might be related to improper hashing of passwords.
05-02-2022 11:00 AM GMT+2 - The incident was escalated to the backend development team.
05-02-2022 11:20 AM GMT+2 - The incident was resolved by updating the backend server's requirements, specifically the bcrypt gem version.
Root Cause and Resolution
The problem was traced back to an outdated version of the bcrypt gem we were using. It was incorrectly flagging a valid hash, which matched what was stored in the database. It seems that the hash we generated was not supported by the installed bcrypt version. Winus successfully resolved the issue by manually updating the bcrypt version in the Gemfile.lock file to a more recent one and reinstalling the required gems, resulting in a successful fix.

Corrective and Preventative Measures
To avoid similar issues in the future, we will implement the following measures:

Continuous Integration Pipeline: Establish a continuous integration pipeline to run builds on each pull request branch. This ensures that builds pass on the pull request branch before merging with the deployment branch.

Monitoring System: Set up a monitoring system for both the database and application servers to proactively track and address any potential issues as they arise.

Testing Protocol: Develop and enforce a testing protocol that mandates passing tests for each new feature before they are merged with the deployment branch. This ensures that new features are thoroughly validated before deployment.
