# Χειροκίνητη ρύθμιση του λογισμικού του Μετεωρολογικού Σταθμού.

Για να ρυθμίσετε το μετεωρολογικό σταθμό δε χρειάζεται προηγούμενη εμπειρία. Χρειάζεται να γίνουν αρκετά βήματα, αλλά τελειώνοντάς τα θα έχετε μάθει αρκετά πράγματα για τους αισθητήρες και το σταθμό. Θα έχετε αποκτήσει μια πρώτη επαφή με τη γραμμή εντολών, το διορθωτή κειμένου nano και τη βάση δεδομένων MySQL. Είναι επίσης μια πολύ καλή εισαγωγή στο Linux.

Αν διαβάσετε τον αρχικό αγγλικό οδηγό θα δείτε ότι προτείνει να συναρμολογήσετε πρώτα το σταθμό και το Raspberry και μετά να ρυμίσετε το λογισμικό.
Σας προτείνω να μην το κάνετε με αυτή τη σειρά, αλλά να ρυθμίστε πρώτα το λογισμικό.


## Χειροκίνητη Εγκατάσταση

1. Ξεκινείστε με μια φρέσκια εγκατάσταση της τελευταίας έκδοσης του [Raspbian](https://www.raspberrypi.org/downloads/raspbian/).
1. Όταν εκκινήσει για πρώτη φορά το Raspberry Pi, θα δείτε την επιφάνεια εργασίας του στα αγγλικά.

1. Από το Μενού πάνω αριστερά, επιλέξτε `Preferences` > `Raspberry Pi Configuration`.
    
1. Σας προτείνουμε να **αλλάξετε τον κωδικό** από το κουμπί που είναι από κάτω.

1. Στην καρτέλα Interfaces, ενεργοποιήστε το I2C:

    ![](images/i2c.png)
    
1. Θα σας ζητηθεί να κάνετε επανεκκίνηση. Επιλέξτε "Yes". 

## Ρύθμιση του ρολογιού (RTC)

Θα κάνουμε την περισσότερη δουλειά από γραμμή εντολών. Ανοίξτε ένα τερματικό, χρησιμοποιώντας το εικονίδιο από τα πάνω εικονίδι ή πατώντας `ctrl`+`alt`+`t`.

   ![](images/terminal.png) 

θα δείτε το παρακάτω:

```bash
pi@raspberrypi: ~ $
```

Μπορείτε τώρα να πληκτρολογήσετε τις παρακάτω εντολές.

Πρέπει, όμως, πρώτα να κατεβάσετε τα απαραίτητα αρχεία: 

```bash
cd ~ && git clone https://github.com/raspberrypi/weather-station
```

Έχουμε συμπεριλάβει ένα σενάριο εγκατάστασης για να ρυθμιστεί αυτόματα το ρολόι πραγματικού χρόνου. Μπορείτε να το εκτελέσετε ή να ακολουθήσετε τις οδηγίες που ακολουθούν για να ρυθμίσετε το ρολόι με το χέρι. Σας προτείνουμε το αρχείο εγκατάστασης!

## Εγκατάσταση ρολογιού

Βεβαιωθείτε πρώτα ότι έχετε τις τελευταίες ενημερώσεις για το Raspberry Pi με τις παρακάτω εντολές:

```bash
sudo apt-get update && sudo apt-get upgrade
```

Θα πρέπει να κάνετε κάποιες αλλαγές σε ένα αρχείο ρυθμίσεωνγια να επιτρέψετε στο Raspberry Pi να χρησιμοποιήσει το ρολόι:

```bash
sudo nano /boot/config.txt
```

Προσθέστε τις παρακάτω γραμμές στο τέλος του αρχείου:

```bash
dtoverlay=w1-gpio
dtoverlay=pcf8523-rtc
```

Πατήστε **Ctrl + O** μετά **Enter** για να αποθηκεύσετε το αρχείο, και **Ctrl + X** για να βγείτε από nano.

Τώρα ρυθμίστε τις απαραίτητες μονάδες να ξεκινούν αυτόματα κατά την εκκίνηση:

```bash
sudo nano /etc/modules
```

Προσθέστε τις παρακάτω γραμμές στο τέλος του αρχείου:

```bash
i2c-dev
w1-therm
```

Πατήστε **Ctrl + O** μετά **Enter** για να αποθηκεύσετε το αρχείο, και **Ctrl + X** για να βγείτε από nano.

For the next steps, we need the Weather Station HAT to be connected to the Raspberry Pi:

```bash
sudo halt
```

Reboot for the changes to take effect:

```bash
sudo reboot
```

Check that the real-time clock (RTC) appears in `/dev`:

```bash
ls /dev/rtc*
```

You should see something like `/dev/rtc0`.

## Initialise the RTC with the correct time

Use the `date` command to check the current system time is correct. If it's correct, then you can set the RTC time from the system clock with the following command:

```bash
sudo hwclock -w
```

If not, then you can set the RTC time manually using the command below (you'll need to change the `--date` parameter, as this example will set the date to the 1st of January 2014 at midnight):

```bash
sudo hwclock --set --date="yyyy-mm-dd hh:mm:ss" --utc
```

For example:

```bash
sudo hwclock --set --date="2015-08-24 18:32:00" --utc
```

Then set the system clock from the RTC time:

```bash
sudo hwclock -s
```

Now you need to enable setting the system clock automatically at boot time. First, edit the rule in `/lib/udev/`:

```bash
sudo nano /lib/udev/hwclock-set
```

Find the lines at the bottom that read:

```bash
if [ yes = "$BADYEAR" ] ; then
    /sbin/hwclock --rtc=$dev --systz --badyear
else
    /sbin/hwclock --rtc=$dev --systz
fi
```

Change the `--systz` options to `--hctosys` so that they read:

```bash
if [ yes = "$BADYEAR" ] ; then
    /sbin/hwclock --rtc=$dev --hctosys --badyear
else
    /sbin/hwclock --rtc=$dev --hctosys
fi
```

Press **Ctrl + O** then **Enter** to save, and **Ctrl + X** to quit nano.

## Remove the fake hardware clock package

Use the following commands to remove the fake hardware clock package:

```bash
sudo update-rc.d fake-hwclock remove
sudo apt-get remove fake-hwclock -y
```

## Testing the sensors

### Install the necessary software packages

Power up your Raspberry Pi and log in.

At the command line, type the following: 

```bash
sudo apt-get install i2c-tools python-smbus telnet -y
```

Test that the I2C devices are online and working:

```bash
sudo i2cdetect -y 1
```

You should see output similar to this:

```
	 0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f
00:          -- -- -- -- -- -- -- -- -- -- -- -- -- 
10: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
20: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
30: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
40: 40 -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
50: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
60: -- -- -- -- -- -- -- -- UU 69 6a -- -- -- -- -- 
70: -- -- -- -- -- -- -- 77                         
```

- `40` = HTU21D, the humidity and temperature sensor.
- `77` = BMP180, the barometric pressure sensor.
- `68` = PCF8523, the real-time clock. It will show as `UU` because it's reserved by the driver.
- `69` = MCP3427, the analogue-to-digital converter on the main board.
- `6a` = MCP3427, the analogue-to-digital converter on the snap-off AIR board.

Note: `40`, `77` and `6a` will only show if you have connected the **AIR** board to the main board.

Now that the sensors are working, we need a database to store the data from them.

## Ρύθμιση Βάσης Δεδομένων

Θα ρυθμίσετε τώρα το σταθμό να αποθηκεύει αυτόματα τα δεδομένα που συνέλλεξε. Τα δεδομένα αποθηκεύονται στην κάρτα SD του Raspberry Pi σε μια βάση δεδομένων που λέγετε MySQL. Μόλις ο σταθμός αποθηκεύσει τα δεδομένα τοπικά, θα μπορείτε επίσης να τα [μεταφορτώσετε](oracle.md) σε μια κεντρική βάση δεδομένωn της Oracle Apex για να τα μοιράζεστε και με άλλους. 

### Εγκατάσταση απαραίτητων πακέτων λογισμικού

Στη γραμμή εντολών πληκτρολογείστε:

```bash
sudo apt-get update
sudo apt-get install apache2 mysql-server python-mysqldb php5 libapache2-mod-php5 php5-mysql -y
```
  
Αν κάνετε κάποιο λάθος, πατήστε το πάνω βελάκι για να επεξεργαστείτε μια προηγούμενη εντολή.

Κατά τη διάρκεια της εγκατάστασης θα σας ζητηθεί να δημιουργήσετε και να επαληθεύσετε έναν κωδικό για το διαχειριστή του εξυπηρετητή βάσης δεδομένων MySQL. Μην τον ξεχάσετε, θα τον χρειαστείτε αργότερα.

### Δημιουργία τοπικής βάσης δεδομένων

Γράψτε την ακόλουθη εντολή:

```bash
mysql -u root -p
```

Βάλτε τον κωδικό που ορίσατε κατά τη διάρκεια της εγκατάστασης του εξυπηρετητή MySql παραπάνω.

Θα δείτε το `mysql>` είστε έτοιμοι να δημιουργήσετε τη βάση δεδομένων:

```mysql
CREATE DATABASE weather;
```

Πρέπει να δείτε `Query OK, 1 row affected (0.00 sec)`.

Ας αλλάξουμε στη βάση αυτή:

```mysql
USE weather;
```

Πρέπει να δείτε `Database changed`.

Αν η MySQL δεν κάνει αυτό που πρέπει ή εμφανίζει λάθη, το πιθανότερο είναι να έχετε ξεχάσει το τελικόy `;`. Απλά πληκτρολογήστε το και πατήστε **Enter**.
  
### Δημιουργία πίνακα στη βάση

Ο πρωτότυπος οδηγός προτείνει να πληκτρολογήσετε τον παρακάτω κώδικα, δίνοντας και τις ακόλουθες συμβουλές: 

- Don't forget the commas at the end of the row.
- Use the cursor UP arrow to copy and edit a previous line, as many are similar.
- Type the code carefully and **exactly** as written, otherwise things will break later.
- Use CAPS LOCK!
  
```
  CREATE TABLE WEATHER_MEASUREMENT(
    ID BIGINT NOT NULL AUTO_INCREMENT,
    REMOTE_ID BIGINT,
    AMBIENT_TEMPERATURE DECIMAL(6,2) NOT NULL,
    GROUND_TEMPERATURE DECIMAL(6,2) NOT NULL,
    AIR_QUALITY DECIMAL(6,2) NOT NULL,
    AIR_PRESSURE DECIMAL(6,2) NOT NULL,
    HUMIDITY DECIMAL(6,2) NOT NULL,
    WIND_DIRECTION DECIMAL(6,2) NULL,
    WIND_SPEED DECIMAL(6,2) NOT NULL,
    WIND_GUST_SPEED DECIMAL(6,2) NOT NULL,
    RAINFALL DECIMAL (6,2) NOT NULL,
    CREATED TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY ( ID )
  );
```

Θα δείτε κάτι σαν `Query OK, 0 rows affected (0.05 sec)`.

Ας το κάνετε λίγο διαφορετικά να γλυτώσετε γράψιμο και λάθη. Επιλέξτε και αντιγράψτε τις παραπάνω εντολές, από το 'CREATE έως το ); . 
Πατήστε  `Ctrl - D` ή πληκτρολογήστε `exit` και **ENTER** για να βγείτε από τη MySQL.

Από γραμμή εντολών γράψτε:
```
nano myscript.sql
```
Στο παράθυρο που θα εμφανιστεί Πατήστε δεξί κλικ και paste. Πρέπει να δείτε κάτι σαν την παρακάτω εικόνα:
![](images/Terminal_063.png) 

Πατήστε **Ctrl + O** μετά **Enter** για να αποθηκεύσετε το αρχείο, και **Ctrl + X** για να βγείτε από nano.

Γράψτε:
```
mysql -u root -p -h localhost weather < myscript.sql
```
Ο πίνακας δημιουργήθηκε στη βάση μας και είναι έτοιμος να δεχτεί τα δεδομένα μας.

## Εγκατάσταση το λογισμικού για τους αισθητήρες

Ξεκινήστε κατεβάζοντας τον κώδικα για την καταγραφή των δεδομένων. Μπορείτε να παραλείψετε αυτό το βήμα αν έχετε ήδη εγκαταστήσει το [ρολόι πραγματικού χρόνου](software-setup.md).

```
cd ~
git clone https://github.com/raspberrypi/weather-station.git
```
Θα δημιουργηθεί ένας νέος φάκελος μέσα στον προσωπικό σας με όνομα `weather-station`.

### Ξεκινήστε το δαίμονα του μετεωρολογικού σταθμού και δοκιμάστε τον

Σύμφωνα με την [Eισαγωγή στο Linux, Ένας πρακτικός οδηγός](http://www.it.uom.gr/teaching/linux/intro-linux-gr/intro-linux.html#sect_04_01), από το Πανεπιστήμιο Μακεδονίας:
> Οι δαίμονες (daemons) είναι διεργασίες διακομιστή που εκτελούνται συνεχώς. Συνήθως αρχικοποιούνται με την έναρξη του συστήματος και μετά περιμένουν στο παρασκήνιο μέχρι να ζητηθεί η συνδρομή τους.

Για να ξεκινήσετε το δαίμονα για το μετεωρολογικό σταθμό, εκτελέστε την παρακάτω εντολή:

```bash
sudo ~/weather-station/interrupt_daemon.py start
```
  
Πρέπει να δείτε κάτι σαν `PID: 2345` (ο αριθμός θα είναι διαφορετικός).
  
Για την παρακολούθηση του μετρητή βροχή και του ανεμόμετρου απαιτείται μια διαδικασία (πρόγραμμα) που εκτελείται συνέχεια. Οι αισθητήρες αυτοί είναι 
μαγνητικοί αισθητήρες reed και ο κώδικας χρησιμοποιεί [διακοπές.](http://www.it.uom.gr/project/mycomputer/r_usage/i_intro.html) 
Οι διακοπές αυτές μπορούν να συμβούν οποιαδήποτε στιγμή, σε αντίθεση με το τις μετρήσεις των άλλων αισθητήρων που γίνονται σε τακτικά χρονικά διαστήματα. 
Μπορείτε να χρησιμοποιήσετε το πρόγραμμα **telnet** για να τα δοκιμάσετε ή να τα παρακολουθήσετε με την ακόλουθη εντολή:
  
```bash
telnet localhost 49501
```
  
Θα πρέπει να δείτε κάτι σαν κι αυτό:

```
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
OK
```

Μπορείτε να χρησιμοποιήσετε τις ακόλουθες εντολές:

- `RAIN`: εμφανίζει τον όγκο βροχής σε ml
- `WIND`: εμφανίζει τη μέση ταχύτητα ανέμου σε χ/ω
- `GUST`: εμφανίζει την ταχύτητα ριπών αέρα σε χ/ω
- `RESET`: μηδενίζει τους μετρητές διακοπής του μετρητή βροχής και του ανεμόμετρου.
- `BYE`: έξοδος

Με την εντολή `BYE` αποσυνδέεστε.
![](images/telnet.png)
Αν ο υπολογιστής σας δεν έχει εγκατεστημένο εφαρμογή telnet, όπως συνήθως συμβαίνει στα windows, Μπορείτε να χρησιμοποιήσετε το [putty](http://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html?).
Είναι ένα ελεύθερο λογισμικό που ενσωματώνει πελάτη telnet και, ίσως ακόμα πιο σημαντικό, πελάτη ssh. Με το putty μπορείτε να συνδεθείτε στο 
raspberrypi απομακρυσμένα μέσω ssh και μετά με telnet, όπως περιγράφεται παραπάνω.

### Set the weather station daemon to automatically start at boot

Use the following command to automate the daemon:

```bash
sudo nano /etc/rc.local
```
  
Insert the following lines before `exit 0` at the bottom of the file:

```bash
echo "Starting Weather Station daemon..."
    
/home/pi/weather-station/interrupt_daemon.py start
```

Press `Ctrl - O` then `Enter` to save, and `Ctrl - X` to quit nano.
    
### Update the MySQL credentials file

You'll need to use the password for the MySQL root user that you chose during installation. If you are **not** in the `weather-station` folder, type:

```bash
cd ~/weather-station
```

then: 

```bash
nano credentials.mysql
```
  
Change the password field to the password you chose during installation of MySQL. The double quotes `"` enclosing the values are important, so take care not to remove them by mistake.
  
Press **Ctrl + O** then **Enter** to save, and **Ctrl + X** to quit nano.

## Automate updating of the database

The main entry points for the code are `log_all_sensors.py` and `upload_to_oracle.py`. These will be called by a scheduling tool called [cron](http://en.wikipedia.org/wiki/Cron) to take measurements automatically. The measurements will be saved in the local MySQL database, and they will also be uploaded to the Oracle Apex database online [if you registered](oracle.md).

You should enable cron to start taking measurements automatically. This is also known as **data logging mode**: 

```bash
crontab < crontab.save
```

Your weather station is now live and recording data at timed intervals.
  
You can disable data logging mode at any time by removing the crontab with the command below:
  
```bash
crontab -r
```
  
To enable data logging mode again, use the command below:
  
```bash
crontab < ~/weather-station/crontab.save
```
  
Please note that you should not have data logging mode enabled while you're working through the lessons in the [scheme of work](https://github.com/raspberrypilearning/weather-station-sow).
  
### Manually trigger a measurement

You can manually cause a measurement to be taken at any time with the following command:

```bash
sudo ~/weather-station/log_all_sensors.py
```
  
Don't worry if you see `Warning: Data truncated for column X at row 1`: this is expected.

  
### View the data in the database 

Enter the following command:

```bash
mysql -u root -p
```
  
Enter the password (the default for the disk image installation is `tiger`). Then switch to the `weather` database:
  
```bash
USE weather;
```
  
Run a select query to return the contents of the `WEATHER_MEASUREMENT` table:

```bash
SELECT * FROM WEATHER_MEASUREMENT;
```

![](images/database.png)
  
After a lot of measurements have been recorded, it will be sensible to use the SQL `where` clause to only select records that were created after a specific date and time:
  
```bash
SELECT * FROM WEATHER_MEASUREMENT WHERE CREATED > '2014-01-01 12:00:00';
```
  
Press **Ctrl + D** or type `exit` to quit MySQL.

## Upload your data to the Oracle Apex database

At this stage, you have a weather station which reads its sensors and stores the data at regular intervals in a database on the SD card. But what if the SD card gets corrupted? How do you backup your data? And how do you share it with the rest of the world?

Oracle has set up a central database to allow all schools in the Weather Station project to upload their data. It is safe there and you can download it in various formats, share it, and even create graphs and reports. Here's how to do it.

### Register your school

You'll need to [register your school](oracle.md) and add your weather station. Come back here when you have your weather station passcode.

### Update credential files with your weather station details

Add the weather station name and password to the local Oracle credentials file with the commands below. This allows the code that uploads to Oracle to add it to the correct weather station.

```bash
cd ~/weather-station

nano credentials.oracle.template
```
  
Replace the `name` and `key` parameters with the `Weather Station Name` and `Passcode` of the weather station above. The double quotes `"` enclosing these values in this file are important, so take care not to remove them by mistake. The weather station name must match exactly and is case-sensitive.
  
Press `Ctrl - O` then `Enter` to save, and `Ctrl - X` to quit nano.
  
Rename the Oracle credentials template file to enable it:

```bash
mv credentials.oracle.template credentials.oracle
```
  
### Checking that data is received

Manually trigger an upload with the following command:

```bash
sudo ~/weather-station/upload_to_oracle.py
```

Log into your school's [Oracle Apex account](oracle.md) and go to 'Weather Measurements'. You should see the station readings:

![](images/weather-readings.png)


You can download your data in various formats and also make charts using the menu:

![](images/wsmenu.png)

## Next steps

- You can now proceed to setting up the rest of the hardware in the [Hardware Setup Section](build2.md)
