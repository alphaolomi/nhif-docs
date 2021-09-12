# 1) Introduction

This document aimed at HMIS[^1] vendors and other integrators. NHIF keeps updating these APIs to cater for new requirements and solve various challenges emanating during the integration process in order to improve service delivery to its beneficiaries as well as other stakeholders. These APIs may require stakeholders to make some tweaks in their systems.

The URLs provided point to the development and testing environment. To go to the live environment you will need to change the URL and remove the `/test/` fragment. 

Examples of test and live URLs are as given below:


TEST : http://196.13.105.15/nhifservice/breeze/

LIVE : https://verification.nhif.or.tz/nhifservice/breeze/


Please take note that the base URL above should be stored in a central repository such as database so that when changed, all the client applications should have the correct URL.


[^1]: Hospital Management Information System (HMIS)