# Alerts
## UpTimeLessThan1Hour
This alert detects when all the instances of the ingress controller have an uptime of less than 1 hour.

## FrontendDown
This alert detects when a frontend has all of its instances down for more than 10 minutes.

## BackendDown
This alert detects when a backend has all of its instances down for more than 10 minutes.

## HighSessionsUsage
This alert triggers when the backend sessions overpass the 85% of the sessions capacity for 10 minutes.

## HigErrorRate
This alert triggers when the error rate is over 15% for over 10 minutes in a proxy.

## HighRequestDeniedRate and HighResponseDeniedRate
The alert detects when a denied rate of requests or responses is over 10% for over 10 minutes in a proxy.

## HighResponseTime
This alert triggers when a proxy has a mean response time higher than 250ms for over 10 minutes.
