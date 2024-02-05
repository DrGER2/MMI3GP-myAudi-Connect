# MMI3GP-myAudi-Connect
Java JAR to fix on-line access to destinations and contacts from a myAudi account.

The file myaudiconnect_nodataconnection.jar copied to /mnt/efs-system/lsd/ when "fair" mode is configured on Harman-Becker MMI3GP systems implements non-GSM connectivity to the myAudi account linked to the VIN of the vehicle for access to on-line contact and destination imports.  When "fair" mode is not configured, an attempt to access contacts or destinations from the linked myAudi account triggers the system to start a PPP data connection through the integrated 3G data modem.  In regions where the 3G data networks are no longer available, such as the U.S., configuring "fair" mode with appropriate network hardware attached to the USB port allow us continued access to various on-line tools, like Google POI search and Google Earth image overlay (using the pass-through service offered by audi-mib.bg).  But access to linked myAudi contacts and destinations has been known to be a problem for at least 10 years.

The solution here was surprisingly simple: the two servers that host the myAudi service named in the properties file bundled in the production myaudiconnect_nodataconnection.jar file supply GZIP encoded content initially, but do not issue the expected "Content-encoding: gzip" header, which the production looks for.  Setting the code to always apply GZIP decompression to the initial input stream solves the connectivity problem.  As a bonus, I confirmed that myAudi VIN/PIN credentials work on both servers: car.audi-online.com (HN+_US) and car.audi-online.de (HN+_EU).

Copy the appropriate .jar file to a working MMI3G SD shell script and apply the following shell script:

```
mount -uw /mnt/efs-system
mv -v /mnt/efs-system/lsd/myaudiconnect_nodataconnection.jar /mnt/efs-system/lsd/myaudiconnect_nodataconnection-ORIG.jar
cp -v myaudiconnect_nodataconnection-XY.jar /mnt/efs-system/lsd/myaudiconnection_nodataconnection.jar
ls -o /mnt/efs-system/lsd/
```

and restart the MMI system.

Credit to sm87 at the AudiWorld.com B8 forum for his many helpful suggestions for reworking the decompiled LeanSoapTransport.class so that it could be recompiled properly with J2SDK 1.4 on our Solaris 11 x64 server. --g
