# Gift Card Code Format Design

Notes on designing the numbers and codes inside a **gift card system**: card number, PIN or security code, check digit, barcode symbology, and how these choices affect fraud, POS entry and customer support. Shared by [MoboGage](https://evdsystem.com/about-mobogage/) as part of the [EVD System](https://evdsystem.com/) educational series on digital vouchers and gift cards.

---

## Two values, two jobs

Most closed-loop gift cards carry two things.

The **card number** identifies the card. It is printed, scanned, and quoted to support. It is not a secret.

The **PIN or security code** proves the holder is entitled to spend it. It is a secret, usually hidden under a scratch panel or delivered only in the email or SMS.

Mixing the two jobs into one code is the most common design mistake. If one printed number both identifies and authorises, anyone who photographs the card on a shelf can spend it after activation.

## Card number choices

A typical structure is a short issuer or programme prefix, an account part, and a check digit:

```
9990 1012 3456 7890 5   (made-up example, 17 digits, valid Luhn)
|  |  |               |
|  |  account part     check digit (Luhn)
|  programme
issuer prefix
```

**Length.** 16 to 19 digits fits most POS keypads and receipt printers. Shorter numbers are easier to read out over the phone but make enumeration easier.

**Check digit.** A Luhn digit catches most single-digit typos and adjacent swaps at the till before a network call is made. It is error detection, not security.

**Sequential vs random account part.** Sequential numbers make batch handling easy but let an attacker guess neighbours. If you keep sequential numbers for logistics, make sure the PIN carries the security.

## PIN choices

**Generated randomly** from a cryptographically secure source, never derived from the card number.

**Length** set against the attack you expect. A 4-digit PIN with no attempt limit is guessable; the same PIN with a strict lockout per card and per terminal is much harder to attack. Online-only codes with no separate PIN need far more entropy, typically 12 or more characters.

**Alphabet.** For alphanumeric codes, drop characters people confuse: 0 and O, 1 and I and L, 5 and S. Customers type codes from screenshots on their phones and support gets the bill for every ambiguity.

**Storage.** Store a hash or encrypt the PIN at rest, and never print it in logs. The same rules that apply to telecom voucher PINs apply here; see the [voucher reconciliation and PIN security](https://evdsystem.com/evd-software/voucher-reconciliation-pin-security-evms/) page.

## Barcode symbology

| Use | Common choice | Note |
|-----|---------------|------|
| Plastic card at retail POS | Code 128 | Widely supported by 1D scanners |
| Mobile wallet pass | QR or PDF417 | Phone screens scan better as 2D |
| Receipt-printed eGift | Code 128 or QR | Check thermal print width |
| Legacy grocery scanners | EAN-13 or UPC-A with a GTIN | Activation via retailer's POS item file |

Encode the card number in the barcode, not the PIN, unless the redemption flow is designed for single-scan use.

## Worked example (illustrative)

A retailer prints 50,000 cards with a 17-digit number and a 4-digit PIN under scratch-off. Redemption allows 3 wrong PINs per card before a 24-hour lock and alerts after 20 failures per terminal per hour. A bot tries 1,000 card numbers with random PINs from one terminal; the terminal alert fires inside the first hour and the terminal is blocked. With no terminal limit, the same attempt would have gone unnoticed until customers complained of empty cards.

The control checklist is in [docs/code-format-checklist.md](./docs/code-format-checklist.md).

---

## FAQ

**What is the difference between a gift card number and a PIN?**
The number identifies the card and is not secret. The PIN proves the right to spend and must be kept hidden until purchase.

**Does a Luhn check digit make gift cards secure?**
No. It only catches typing errors. Security comes from a random PIN and attempt limits.

**Which barcode should a gift card use?**
Code 128 is the usual choice for plastic cards at POS. QR or PDF417 suits phone screens and wallet passes.

**How long should an online gift card code be?**
If there is no separate PIN, use a long random code, often 12 characters or more from an unambiguous alphabet, plus rate limiting.

---

## Further reading

- [What is an electronic voucher](https://evdsystem.com/evd-software/what-is-an-electronic-voucher/) on evdsystem.com
- [Voucher reconciliation and PIN security](https://evdsystem.com/evd-software/voucher-reconciliation-pin-security-evms/) on evdsystem.com
- Related repos: [gift-card-fraud-prevention](https://github.com/iwtglobal/gift-card-fraud-prevention), [voucher-batch-generation](https://github.com/iwtglobal/voucher-batch-generation)

## License

MIT. See [LICENSE](./LICENSE). Educational material only.
