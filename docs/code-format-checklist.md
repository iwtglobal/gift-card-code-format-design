# Gift Card Code Format Checklist

## Card number

- [ ] Fixed length that fits POS keypads and receipts
- [ ] Programme prefix documented and registered internally
- [ ] Luhn check digit validated at POS before the network call
- [ ] Decision recorded on sequential vs random account part

## PIN / security code

- [ ] Generated from a CSPRNG, independent of card number
- [ ] Length chosen against the attempt limits you will actually enforce
- [ ] Unambiguous alphabet for alphanumeric codes
- [ ] Hashed or encrypted at rest, masked in logs and exports

## Redemption limits

- [ ] Wrong-PIN lockout per card
- [ ] Failure alert per terminal and per API key
- [ ] Lock and alert events visible to the fraud team

## Barcode

- [ ] Symbology tested on the retailer's actual scanners
- [ ] Barcode carries the card number, not the PIN
- [ ] Print test at the real thermal width
