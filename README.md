# Signed YAML 0.8

This is a technical descriptor of a constructed file which is a [YAML][yaml]
stream that has been signed using a public/private key (e.g. RSA).

## License

This specification and document is freely released under the
[Very Open License](http://veryopenlicense.com).

## YAML Requirements

A valid [YAML][yaml] stream (`1.0`, `1.1`, or `1.2`) is first constructed, with
the following requirements:

1. The YAML stream **must** start with three dashes (e.g., `---`) as shown
	in [YAML 1.0][yaml0dash], [YAML 1.1][yaml1dash], and [YAML 1.2][yaml2dash].
2. The YAML stream **must** be terminated with the three periods
	(e.g. `...`) as shown in [YAML 1.0][yaml0dots], [YAML 1.1][yaml1dots],
	and [YAML 1.2][yaml2dots].
3. Characters prior to the stream-staring three dashes (`---`) and after
	the stream-ending three periods (`...`) are **not** allowed.
4. YAML multi-document streams **are** allowed, so long as they conform
	to the other requirements of this document.

## YAML Stream

Within these specifications, the *YAML stream* means the exact, literal UTF-8
characters starting at and including the stream-start (e.g., `---`), and
ending with and including the stream-end (e.g., `...`). Note that this
includes non-data characters, such as comments.

## Stream Digest

The message digest of the YAML stream is called the *stream digest*. This digest
is the SHA-256 hash, as defined in [FIPS PUB 180-2][fips], generated from the
exact characters of the YAML stream.

## Digest Signature

The stream digest signature is generated using the RSASSA-PSS method specified
in [RFC3447][rsapss]. The private RSA key used **must** be at least 2048 bits.

## Digest Signature Encoding

The output signature of the RSASSA-PSS digest function is serialized
using the base64 format as defined by [RFC 2045][rfc2045] (MIME), with
the following changes:

* The content is **not** restricted to lines of 76 characters or less.
* Characters other than the base64 alphabet and line breaks are
	considered **an error**.

## Digest Signature Placement

The encoded signature is placed at the beginning of the file, followed by
a single line break, and the YAML stream is placed immediately after.

There is no required length at which the signature characters must be
split, but it is recommended that they be split before or at 80 characters.

## Complete File

The final SYML file has the characters ordered as follows:

1. The encoded stream digest signature, split with a line break at an
	appropriate line length.
2. After the last character of the encoded stream digest signature,
	another line break.
3. The three dashes (`---`) indicating the start of the YAML stream.
4. The complete contents of the YAML stream
5. The three periods (`...`) indicating the end of the YAML stream.

## Signature Verification

To verify the signature of the SYML file, the stream digest is generated.
This digest is the *message* fed into the [RSASSA-PSS-VERIFY][verify]
function to verify it using the public key of the key-pair used to
sign the YAML stream.

## File Integrity

Implementations of SYML file readers should include the
signature verification as part of the file opening process. If the
signature is found to be invalid, or if the application opening the
file does not have the public key required for proper verification,
this is a security risk and the user should be alerted or informed
wherever possible.

## Example

An example of a SYML file might look like:

	OS04M3FodDAtOGhxLTlyOGhnLTlhOGhmZy05c2RmaGdqWpkZjA5amFzZGYw
	OWphMDlqZjA5amRmc2QAtOGhxLTlyOGhnLTlhOGhmZy05c2RmaGdqOS04M3
	FodDjA5amFzZGYwOWphMDlqZjA5amRmc2QWpkZ5c2RmaGdqOS04M3FodDAt
	ZWZpb2pzMDlkZnUtMGVqZmFwc29kamZhczlkZmMGFzOjBqZjA5MmplMGY5a
	mEwczlqZGYwOWphczA5ZGpjBqZjA5MmplMGY5amEwczlqZGYwOWphczA5ZG
	pmMGFzO5amFzZGYwOWphMDlqZjA5amRmc2QWpkZjA=
	---
	receipt: Oz-Ware Purchase Invoice
	date:    2012-08-06
	customer:
	  given:   Dorothy
	  family:  Gale
	...

[yaml]: http://www.yaml.org/
[yaml0dots]: http://www.yaml.org/spec/1.0/#id2489959
[yaml1dots]: http://www.yaml.org/spec/1.1/#id857577
[yaml2dots]: http://www.yaml.org/spec/1.2/spec.html#id2760395
[yaml0dash]: http://yaml.org/spec/1.0/#id2489959
[yaml1dash]: http://yaml.org/spec/1.1/#id857577
[yaml2dash]: http://www.yaml.org/spec/1.2/spec.html#id2760395
[fips]: http://csrc.nist.gov/publications/fips/fips180-2/fips180-2.pdf
[radix64]: http://tools.ietf.org/html/rfc4880
[rfc2045]: http://tools.ietf.org/html/rfc2045
[rsapss]: https://tools.ietf.org/html/rfc3447#page-29
[verify]: https://tools.ietf.org/html/rfc3447#section-8.1.2
