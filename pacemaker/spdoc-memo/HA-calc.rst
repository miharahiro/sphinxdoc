============================================
Calculations of high availability systems 
============================================

Motivation
================

HA Proxy and/or Pacemaker seem to provide us high availability of the system. But how much available? Quantitative analysis, or calculations of the availability of the system using HA Proxy and Pacemaker are shown.


Availability Calculations
============================

System Layout
-----------------

HA Proxy x M in front and Web x N as back-end.

Definition of probability
---------------------------------

P_HAP : Probability of HA Proxy server's availability. (0 - 1.0) '1' means always available.

P_Web : Probability of back-end Web server's availability. (0 - 1.0)

Probability Calculations
---------------------------

System is down when,

- (All the HA Proxies are down) or ((At least one of the HA proxy is up) and (All the Web servers are down))

  - (All the HA Proxies are down) => (1 - P_HAP)^M
  - (At least one of the HA proxy is up) => (1 - (1 - P_HAP)^M)
  - (All the Web servers are down) => (1 - P_Web)^N

- So the probability of the system down equals

.. math::

   (1 - P_{HAP})^M + (1 - (1 - P_{HAP})^M) x (1 - P_{Web})^N

- Availability of the system is thus,

.. math::
   1 - {(1 - P_{HAP})^M + (1 - (1 - P_{HAP})^M) x (1 - P_{Web})^N}
    = (1 - (1 - P_{HAP})^M) x (1 + (1 - P_{Web})^N)
