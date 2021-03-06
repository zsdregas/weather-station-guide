# Δοκιμάζοντας τον Μετεωρολογικό Σταθμού

Με όλους τους αισθητήρες συνδεδεμένους, είναι καλό να βεβαιωθείτε ότι ο μετεωρολογικός σταθμός σας καταγράφει δεδομένα και ότι μπορεί να το ανεβάσει στη βάση Oracle.

## Χειρίζοντας τους αισθητήρες
1. Το ανεμόμετρο και το βροχόμετρο μπορούμε να χειριστούμε με το χέρι καθώς κανένα απο τα δύο δεν καταγράφει δεδομένα εκτός αν κινηθούν φυσικά. Στρίβοντας το ανεμόμετρο και γέρνοντας το βροχόμετρο μπρός και πίσω μερικές φορές καθώς ακολουθείτε τα παρακάτω βήματα θα έχει ως αποτέλεσμα να καταγραφούν δεδομένα. 

1. Ανοίξτε ένα τερματικό παράθυρο (**ctrl** + **alt** + **t**) και πηγαίνουμε στο φάκελο `weather-station`:

  ```bash
  cd weather-station
  ```

1. Για να αρχίσουμε την καταγραφή των δεδομένων, πληκτολογούμε την ακόλουθη εντολή στο τερματικό:

  ```bash
  ./log_all_sensors.py
  ```

1. Θα δείτε την έξοδο όπως την δείχνουμε παρακάτω.

  ![](images/test_01.png)

1. Μην ανησυχήσετε με τα μηνύματα `Data truncated`. Οι αισθητήρες μετρούνται σε ένα απίστευτο αριθμό σημαντικών ψηφίων, οπότε αυτές οι αξίες περικόπτονται πριν προσθεθούν στη βάση δεδομένων.

## Ανέβασμα στην Oracle

1. Το επόμενο βήμα είναι να ελεγχθεί ότι το λογισμικό είναι ικανό να ανεβάσει δεδομένα στην online βάση δεδομένων της Oracle. Για να το ελέγξετε αυτό, πληκτρολογήστε τα παρακάτω μέσα στο τερματικό:

  ```bash
  sudo ./upload_to_oracle.py
  ```

1. Θα δείτε την έξοδο όπως την δείχνουμε παρακάτω.

  ![](images/test_02.png)

1. Κάθε απόκριση `Response status: 201` μήνυμα σημαίνει ότι μια γραμμή τοπικής βάσης δεδομένων του Raspberry Pi έχει ανεβεί στη βάση δεδομένων της Oracle. Αν λάβετε ένα διαφορετικό κωδικό απόκρισης, τότε ελέξτε αν το Raspberry Pi είναι συνδεδεμένο στο δίκτυο και ότι είναι ικανό να επικοινωνήσει μέσα από τοίχη προστασίας ή διακομιστές μεσολάβησης που μπορεί να χρησημοποιεί το δίκτυο σας.

## Ελέχοντας την online βάση δεδομένων

1. Στο φυλλομετρητή σας, εξερευνήστε στην [Βάση Δεδομένων της Oracle](https://apex.oracle.com/pls/apex/f?p=81290:LOGIN_DESKTOP:0:::::&tz=1:00) και συνδεθείτε:

  ![](images/test_03.png)

1. Στο ταμπλό σας, κάντε κλικ στο Weather Measurement για να δείτε την πιο πρόσφατη μέτρηση απο το μετεωρολογικό σταθμό σας.

  ![](images/test_04.png)

1. Είναι καλό να 'παίξετε' με τα φίλτρα και της επιλογές λήψης με τα δεδομένα, για να δείτε τη μπορεί να επιτευχθεί.

## Επόμενο;

Τώρα που έχετε δοκιμάσει το μετεωρολογικό σας σταθμό, κλείστε τα κουτιά με τις μακριές βίδες και βεβαιωθείτε ότι τα πλαστικά είναι στην θέση τους. 

  ![](images/close_up_station.png)

Μετά μπορείτε να προχωρήσετε στο επόμενο κεφάλαιο, και μάθετε πως να [εγκαταστήσετε τον Μετεωρολογικό Σταθμό στο σχολείο σας](siting.md).