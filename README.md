# adsb-auth-gnss
Georgia Tech Online Masters in Cybersecurity Capstone - ECE6727 - ADSB with Authenticated GNSS

This project consists of 3 applications:
1. A mock GNSS receiver `gnssrecv`,
2. A mock ADS-B transponder `adsbtrans`, and
3. A mock ADS-B receiver `adsbrecv`

The mock GNSS receiver uses the `galmon` and `osnma-galileo` submodules to retrieve, parse, and authenticate the OSNMA content from Galileo INAV signals as received by a u-blox GNSS receiver. If the OSNMA messages are successfully authenticated using the Galileo OSNMA Merkle Tree and Public Key published by the European GNSS Service Centre (https://www.gsc-europa.eu/), mock latitude/longitude/altitude position reports are signed by the mock GNSS receiver and sent to the mock ADS-B transponder for broadcasting. The mock ADS-B receiver then receives ADS-B messages broadcast by the transponder, and uses the mock GNSS receiver public key to verify that the signed position reports came from a GNSS receiver that only uses authenticated GNSS signals.

There are three test scenarios:
1. The Galileo OSNMA content fails authentication
   - This indicates a possible GNSS spoofing attempt, and the expected outcome is for the GNSS receiver to ignore these GNSS signals for calculating the current position. If there are no GNSS signals for which the OSNMA content passes authentication, the corresponding position report from the GNSS receiver should indicate that the satellite signals used to calculate it are not authenticated and therefore may be untrustworthy.
2. The Galileo OSNMA content passes authentication, but the signed position report embedded in the ADS-B message received by the ADS-B receiver fails authentication
   - This indicates an uncertified GNSS receiver is being used to produce the position reports for the ADS-B transponder, and the expected outcome is for the ADS-B receiver to reject these position reports as they may be untrustworthy. The ADS-B receiver may still display this information to the user while making clear that the data is unverified.
3. The Galileo OSNMA content passes authentication, and the signed position report embedded in the ADS-B message received by the ADS-B receiver passes authentication
   - This indicates that the full chain of trust has been established from the ADS-B receiver to GNSS receiver to the satellite system, and the ADS-B messages can be fully trusted. The cryptographic signatures involved ensure that it would be infeasible for a malicious actor to compromise the integrity of the data at any point along this chain.
