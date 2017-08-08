## Design Communication Protocal

### Why design communication protocal

TCP is connection oriented. Once established, its data can be sent bidirectional. It rearranges data packets in the order specified. Unlike UDP, the data transported by TCP is read as a “stream,” with nothing distinguishing where one packet ends and another begins. And there may be multiple packets per read call.

When the applications want to use TCP to exchange data, it needs to use or design the protocal over TCP to solve the problem of distinguishing packets. There are many commonly used protocals over TCP, for instance, HTTP, FTP, SMTP, POP3, etc.

Except for those commonly used protocals, you can design customized protocal according to your application. For designing protocal, the most important is how to distinguish the boundary between packets. There are two examples of customized protocal.

### Customized Protocal

#### EOF terminator protocal

This protocal adds a specified string in the end of every packet to represent the boundary of the packet. For example, the memcache, ftp and stmp use the `\r\n` as terminator. If the protocal use the `\r\n` as terminator, there mustn't be `\r\n` in the data.

It is easy to use EOF terminator protocal in swoole by setting the corresponding parameters.

```php
$server->set(array(
	'open_eof_split' => true,
	'package_eof' => "\r\n",
));

$client->set(array(
	'open_eof_split' => true,
	'package_eof' => "\r\n",
));

```

#### Fixed packet head and body protocal

This protocal devides the packet to two parts, head and body. The length of head is fixed. And one of parameter in the head indicates the whole length of the packet. When the server receives this kind of packet, it can control the length of reading from the data stream according to the head.

It is easy to set this kind of protocal in swoole by setting the corresponding parameters. When this kind of protocal is setted, the receive event isn't triggered until the server or client receive a full packet.

```php
$server->set(array(
	'open_length_check' => true,
	'package_max_length' => 81920,
	'package_length_type' => 'n', //see php pack()
	'package_length_offset' => 0,
	'package_body_offset' => 2,
));
```
