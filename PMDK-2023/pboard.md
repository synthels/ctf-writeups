# ΠΜΔΚ 2023 - PBoard

Ξεκινάμε αναλύοντας το `.rodata` section του `updater` με το `objdump`. Έτρεξα την ακόλουθη εντολή:

```sh
$ objdump -sj.rodata updater

updater:     file format elf64-x86-64

Contents of section .rodata:
 2000 01000200 00000000 03000000 00000000  ................
 2010 7472656c 6c6f2e63 6f6d002f 646f776e  trello.com./down
 2020 6c6f6164 2f6c6174 6573742d 76657273  load/latest-vers
 2030 696f6e2e 6a736f6e 00000000 00000000  ion.json........
 2040 2f312f63 61726473 2f363366 39363531  /1/cards/63f9651
 2050 65616532 34613964 30313465 39613239  eae24a9d014e9a29
 2060 64000000 00000000 2f617474 6163686d  d......./attachm
 2070 656e7473 2f363366 39363534 31613961  ents/63f96541a9a
 2080 62373537 66306662 34633535 33006d61  b757f0fb4c553.ma
 2090 6c6c6f63 28292066 61696c65 640a0072  lloc() failed..r
 20a0 65616c6c 6f632829 20666169 6c65640a  ealloc() failed.
 20b0 00000000 00000000 75736572 2d616765  ........user-age
 20c0 6e743a20 50426f61 72642055 70646174  nt: PBoard Updat
 20d0 65722076 302e3200 76657273 696f6e00  er v0.2.version.
 20e0 6d616a6f 72006d69 6e6f7200 6368616e  major.minor.chan
 20f0 67656c6f 67000000 54686572 65206973  gelog...There is
 2100 2061206e 65772076 65727369 6f6e2025   a new version %
 2110 642e2564 20617661 696c6162 6c65210a  d.%d available!.
 2120 00436861 6e67656c 6f673a0a 25730a00  .Changelog:.%s..
 2130 54686520 63757272 656e746c 7920696e  The currently in
 2140 7374616c 6c656420 76657273 696f6e20  stalled version
 2150 25642e25 64206973 20746865 206c6174  %d.%d is the lat
 2160 65737420 61766169 6c61626c 652e0a00  est available...
 2170 46617461 6c204572 726f7221 20506c65  Fatal Error! Ple
 2180 61736520 636f6e74 61637420 43544620  ase contact CTF 
 2190 61646d69 6e732120 28657272 6f722063  admins! (error c
 21a0 6f646520 2564290a 00                 ode %d)..
```

Παρατηρούμε το, λίγο παράξενο, **πολύ** out of place trello link. Με λίγες δοκιμές, βρίσκουμε πως είναι το `https://trello.com/1/cards/63f9651eae24a9d014e9a29d`. Εκεί βρίσκουμε το ακόλουθο JSON αρχείο:

```json
{
  "id": "63f9651eae24a9d014e9a29d",
  "badges": {
    "attachmentsByType": {
      "trello": {
        "board": 0,
        "card": 0
      }
    },
    "location": false,
    "votes": 0,
    "viewingMemberVoted": false,
    "subscribed": false,
    "fogbugz": "",
    "checkItems": 0,
    "checkItemsChecked": 0,
    "checkItemsEarliestDue": null,
    "comments": 0,
    "attachments": 1,
    "description": true,
    "due": null,
    "dueComplete": false,
    "start": null
  },
  "checkItemStates": [],
  "closed": false,
  "dueComplete": false,
  "dateLastActivity": "2023-02-25T01:38:34.062Z",
  "desc": "Host the latest version information here so that it is easier to change the info on the fly.",
  "descData": {
    "emoji": {}
  },
  "due": null,
  "dueReminder": null,
  "email": null,
  "idBoard": "63f964ffd03f01c6f14ab44e",
  "idChecklists": [],
  "idList": "63f964ffd03f01c6f14ab457",
  "idMembers": [],
  "idMembersVoted": [],
  "idShort": 1,
  "idAttachmentCover": null,
  "labels": [],
  "idLabels": [],
  "manualCoverAttachment": false,
  "name": "New Version is out!",
  "pos": 65535,
  "shortLink": "9evAIOPK",
  "shortUrl": "https://trello.com/c/9evAIOPK",
  "start": null,
  "subscribed": false,
  "url": "https://trello.com/c/9evAIOPK/1-new-version-is-out",
  "cover": {
    "idAttachment": null,
    "color": null,
    "idUploadedBackground": null,
    "size": "normal",
    "brightness": "dark",
    "idPlugin": null
  },
  "isTemplate": false,
  "cardRole": null
}
```

Ακολουθούμε το `https://trello.com/c/9evAIOPK`, το οποίο βρίσκεται στο πεδίο `shortUrl`. Στον πίνακα "To Do", βλέπουμε την κάρτα "Database backup", στην οποία υπάρχει σαν συνημμένο ένα αρχείο `csv`. Σε αυτό το αρχείο βρίσκουμε πολύ διακριτικά κρυμμένο το πολυπόθητο μας flag.

~ synthels
