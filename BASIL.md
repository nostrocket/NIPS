### BASIL: FASTEST ZAP SPLITS EVER

#### PROTOCOL FLOW
Alice creates a zap split request event and sends it to 

#### SPLIT REQUEST EVENT

```
{
  "kind": 9738,
  "content": "Zap SPLIT!",
  "tags": [
    ["relays", "wss://nostr-pub.wellorder.com", "wss://anotherrelay.example.com"],
    ["amount", "3112"], //includes a fee of 2 sats
    ["payment_hash", "dcdd21bc588bfa26d448a320d94063ef9a2e83345778866707761912bae53fe5"],
    ["p", "04c915daefee38317fa734444acee390a8269f5e6dd343dfbecc9e5810b2241e", "1000"],
    ["p", "e38317fa73444404c915daefeacee390a8269fe5810b2241e5e6dd343dfbecc9", "1000"],
    ["p", "44404c915daefeacee390a8269fe5810b2241e5e6dd343dfbecc9e38317fa734", "1110"],
    ["e", "9ae37aa68f48645127299e9453eb5d908a0cbb6058ff340d528ed4d37c8994fb"]
  ],
  "pubkey": "97c70a44366a6535c145b333f973ea86dfdc2d7a99da618c40c64705ad98e322",
  "created_at": 1679673265,
  ...
}
```

#### ZAP SPLIT RECEIPT
```
{
    "pubkey": "9630f464cca6a5147aa8a35f0bcdd3ce485324e732fd39e09233b1d848238f31",
    "created_at": 1674164545,
    "kind": 9739,
    "tags": [
      ["p", "04c915daefee38317fa734444acee390a8269f5e6dd343dfbecc9e5810b2241e", "1000"],
    ["p", "e38317fa73444404c915daefeacee390a8269fe5810b2241e5e6dd343dfbecc9", "1000"],
    ["p", "44404c915daefeacee390a8269fe5810b2241e5e6dd343dfbecc9e38317fa734", "1110"],
    ["e", "9ae37aa68f48645127299e9453eb5d908a0cbb6058ff340d528ed4d37c8994fb"],
      ["bolt11", "lnbc10u1p3unwfusp5t9r3yymhpfqculx78u027lxspgxcr2n2987mx2j55nnfs95nxnzqpp5jmrh92pfld78spqs78v9euf2385t83uvpwk9ldrlvf6ch7tpascqhp5zvkrmemgth3tufcvflmzjzfvjt023nazlhljz2n9hattj4f8jq8qxqyjw5qcqpjrzjqtc4fc44feggv7065fqe5m4ytjarg3repr5j9el35xhmtfexc42yczarjuqqfzqqqqqqqqlgqqqqqqgq9q9qxpqysgq079nkq507a5tw7xgttmj4u990j7wfggtrasah5gd4ywfr2pjcn29383tphp4t48gquelz9z78p4cq7ml3nrrphw5w6eckhjwmhezhnqpy6gyf0"],
      ["description", "<embedded zap split request>"],      
      ["preimage", "67b48a14fb66c60c8f9070bdeb37afdfcc3d08ad01989460448e4081eddda446"]
    ],
    "content": "",
  }
```

