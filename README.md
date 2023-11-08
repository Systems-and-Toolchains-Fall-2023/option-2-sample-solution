[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-24ddc0f5d75046c5622901739e7c5dd533143b0c8e959d652212380cedb1ea36.svg)](https://classroom.github.com/a/yDpbj8_M)

# Project 2 - MQTT Dataset Analysis and Modeling

## Task 1

### Identify constraints as needed and document them in your Readme.md file. 

As each column in the dataset can repeat, a new column must be created to implement a PRIMARY KEY constraint such that each row’s entry in that column is UNIQUE and NOT NULL. Creation of this new column serves as an index for the dataframe. Another way to implement a time stamp would be to generate a new column (e.g. time_elapsed) based on a running total of tcp.time, which should have NOT NULL, UNIQUE, and CHECK(time_elapsed > 0) constraints.
Since all the other entries can repeat, there should be no UNIQUE constraint for each column, but NOT NULL should apply for all columns. For purposes of pattern recognition in continuous data, all columns must have no null values. If any columns have more than 50% null values, which there are none in the MQTT dataset, then those columns would be dropped.

As columns containing flag information follow the format ‘0x000000##’, where # can be a character or a digit, a CHECK(char_length(flag_col) = 10) constraint should be applied such that the string format is enforced for [tcp.flags, mqtt.conack.flags, mqtt.hdrflags]. Given that information packets from sensors are sequentially sent in real time, the column tcp.time_delta must strictly be positive by applying a CHECK(tcp.time_delta > 0) constraint. The five columns recording the length of the messages [tcp.len, mqtt.len, mqtt.proto.len, ‘mqtt.willmsg.len’, ‘mqtt.willtopic.len’] also must strictly be positive with a CHECK(name.len > 0) constraint.

As part of phase 2 in data engineering, variable classification via one-hot-encoding can be applied to the following columns: [tcp.flags, mqtt.hdrflags, target], where each must be NOT NULL as specified before. Based on the schema [mqtt.conack.flags, mqtt.conflags, mqtt.hdrflags, mqtt.msg, mqtt.protoname] are strings, but mqtt.conack.flags, mqtt.conflags, and mqtt.msg are mostly comprised of double types that equal 0. The column mqtt.msg is peculiar: its 7700th row and 25928th row contain the string “6d6f7371756974746f2076657273696f6e20312e362e39”, while >99% of the other entries contain a double variable type value. 
For these cases, data cleanup needs to be applied via imputation or deletion, which is outside the scope of constraints. 

### In your ReadMe.md, add a description for the features in the dataset. 

MQTT, or Message Queue Telemetry Transport, dataset exists to bridge the gap between datasets on network traffic (KDDCUP99, NIMS, NLS-KDD) and datasets that focus on IoT but are missing raw traffic and packet capturing streams. To connect eight smart devices, each with a unique IP address, across two rooms in the proposed IoT home, a MQTT broker enables a thermostat, light, humidity, CO2, smoke, and motion sensor, fan, and door lock to communicate with each other. No firewall exists in this network, which makes it susceptible to attacks when a malicious node connects to it.

The three columns with “tcp” stand for Transmission Control Protocol, which connects an application with an IP address on the network. The next four columns with “mqtt.conack” refer to connection acknowledgement messages, which are metadata describing connection of devices to the network. Following those, the next eight columns with "conflag" describe the details of a specific session for a connected device. The next 18 columns with "mqtt" contain information related to the communication between devices on the network. Finally, the 34th column "target" denotes whether an event was one of five types of attacks or part of legitimate traffic.

Below is a table that summarizes the features:

| No. | Name | Description | Protocol | Data Type |
| :---: |  :---: |  :---: |  :---: |  :---: | 
| 1 | tcp_flags | TCP flags | TCP | char string | 
| 2 | tcp_time_delta | Time TCP stream | TCP | float | 
| 3 | tcp_len | TCP Segment Len | TCP | unsigned int |
| 4 | mqtt_conack_flags | Acknowledge Flags | MQTT | unsigned int |
| 5 | mqtt_conack_flags_reserved | Reserved | MQTT | boolean |
| 6 | mqtt_conack_flags_sp | Session Present | MQTT | boolean |
| 7 | mqtt_conack_val | Return Code | MQTT | unsigned int |
| 8 | mqtt_conflag_cleansess | Clean Session Flag | MQTT | boolean |
| 9 | mqtt_conflag_passwd | Password Flag | MQTT | boolean |
| 10 | mqtt_conflag_qos | QoS Level | MQTT | unsigned int |
| 11 | mqtt_conflag_reserved | (Reserved) | MQTT | boolean |
| 12 | mqtt_conflag_retain | Will Retain | MQTT | boolean |
| 13 | mqtt_conflag_uname | User Name Flag | MQTT | boolean |
| 14 | mqtt_conflag_willflag | Will Flag | MQTT | boolean |
| 15 | mqtt_conflags | Connect Flags | MQTT | unsigned int |
| 16 | mqtt_dupflag | DUP Flag | MQTT | boolean |
| 17 | mqtt_hdrflags | Header Flags | MQTT | unsigned int |
| 18 | mqtt_kalive | Keep Alive | MQTT | unsigned int |
| 19 | mqtt_len | Msg Len | MQTT | unsigned int |
| 20 | mqtt_msg | Message | MQTT | byte sequence |
| 21 | mqtt_msgid | Message Identifier | MQTT | unsigned int |
| 22 | mqtt_msgtype | Message Type | MQTT | unsigned int |
| 23 | mqtt_proto_len | Protocol Name Length | MQTT | unsigned int |
| 24 | mqtt_protoname | Protocol Name | MQTT | char string |
| 25 | mqtt_qos | QoS Level | MQTT | unsigned int |
| 26 | mqtt_retain | Retain | MQTT | boolean |
| 27 | mqtt_sub_qos | Requested QoS | MQTT | unsigned int |
| 28 | mqtt_suback_qos | Granted QoS | MQTT | unsigned int |
| 29 | mqtt_ver | Version | MQTT | unsigned int |
| 30 | mqtt_willmsg | Will Message | MQTT | byte sequence |
| 31 | mqtt_willmsg_len | Will Message Length | MQTT | unsigned int |
| 32 | mqtt_willtopic | Will Topic | MQTT | char string | 
| 33 | mqtt_willtopic_len | Will Topic Length | MQTT | unsigned int |
| 34 | target | Activity Classification | N/A | char string | 
