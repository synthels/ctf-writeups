# ΠΜΔΚ 2023 - WiFi πειστήρια

Ανοίγουμε τα δυο `pcap` αρχεία στο Wireshark, για να δούμε τι παίζει τελικά με αυτόν τον μαφιόζο. Αφού συνέλθουμε από το άθλιο UI που διέπει δυστυχώς τα περισσότερα OSS, παρατηρούμε πως η συνομιλία Router-συσκευής είναι κρυπτογραφημένη (μάλλον είναι ανοιχτό το WPA). Δοκιμάζουμε λοιπόν να κάνουμε bruteforce το password.

```sh
aircrack-ng -w ./rockyou.txt capture-timeslot-2.pcap
```

(το rockyou.txt το πήρα απο [εδώ](https://github.com/brannondorsey/naive-hashcat/releases/download/data/rockyou.txt)). Θαύμα! Θαύμα! Το `aircrack-ng` μας δίνει το password `lamborghini` σε λιγότερο από 1 δευτερόλεπτο! Πάμε πίσω στο Wireshark λοιπόν, βάζουμε το καινούργιο μας password στο `wpa-pwd` και μπορούμε πλέον να δούμε όλα τα πακέτα όπως θα ήθελε ο Θεός. Μεταξύ των irrelevant πακέτων, εντοπίζουμε ένα `GET` request για το `chat.js`, και ακριβώς από κάτω ολόκληρο το `chat.js`. Στο `chat.js` διαπιστώνουμε πως η επικοινωνία γίνεται με Websockets, ενώ υπάρχει ένα λεπτό στρώμα κρυπτογράφησης με την μορφή ενός XOR cipher.

Από τα Websocket πακέτα που έχουμε στα `pcap`, τα σημαντικά (με μηνύματα και `state`) βρίσκονται μόνο στο `capture-timeslot-1.pcap`. Από αυτά θα πάρουμε μόνο το μήνυμα

```js
let msg = [159,197,139,45,5,38,112,119,206,31,211,99,32,93,221,202,26,178,18,89,148,247,99,65,80,220,31,156,120,146,70,125,187,235,255,11,78,37,38,32,144,65,140,107,33,80,219,152,29,181,16,88,151,162,101,20,83,134,27,146,115,146,23,33,224,190,175,12,27,58]
```

και το `state`, το οποίο βρίσκεται σε όλα τα μηνύματα με τύπο `init`.

```
181dcff2143dea315065aef7c898f551859fdbd40b3661ee181356ade8123dd2
```

Η "κρυπτογράφηση" που χρησιμοποιείται είναι απλοϊκή, και περιορίζεται σε ένα XOR cipher με κλειδί μια (πολλές φορές) SHA256 hashed έκδοση του `state` (που έβαλα ακριβώς πιο πάνω). Για την ακρίβεια, προς το τέλος βλέπουμε τις γραμμές:

```js
case 'message':
  state = hash(state);
  el.state.textContent = state;
  data.message = decrypt(state, data.message);
  message_received(data.name, data.message);
  break;
```

Και απ'οτι φαίνεται ο τύπος κάνει hash το hash του hash ώ hash το `state` *κάθε* φορά που λαμβάνει ένα καινούργιο μήνυμα. Συνεπώς, χρησιμοποιώντας την `decrypt` που βρίσκεται λίγο πιο πάνω:

```js
function xorcrypt(key, value) {
    key = hexToBytes(key);
    while (key.length < value.length) {
        key = [...key, ...key];
    }
    for (let i = value.length - 1; i >= 0; i--) {
        value[i] = value[i] ^ key[i];
    }
    return value;
}

function decrypt(key, value) {
    value = xorcrypt(key, value);
    return new TextDecoder().decode(new Uint8Array(value).buffer);
}
```

Δοκιμάζουμε απανωτά hash στο `state` για να βρούμε το κλειδί, το οποίο βρήκα μετά από 4-5 (δοκιμάζοντας κάθε φορά να αποκρυπτογραφήσω το `msg`). Τελικά το κλειδί ήταν το `d989ca6a7e474014f627b5531864b8a82d842468a091527235bf2eab41a42244`. Pwned?

~ synthels
