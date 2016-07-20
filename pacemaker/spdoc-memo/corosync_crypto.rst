===================================
Crypt corosync/totem messaging
===================================

- Corosync/Totem messages are encrypted with the following configurations in /etc/corosync/corosync.conf ::
    
    crypto_hash: sha1
    crypto_cipher: aes256

  From man of corosync.conf ::

    crypto_hash
              This specifies which  HMAC  authentication  should  be  used  to
              authenticate all messages. Valid values are none (no authentica‐
              tion), md5, sha1, sha256, sha384 and sha512.

              The default is sha1.

    crypto_cipher
              This specifies which cipher should be used to encrypt  all  mes‐
              sages.   Valid  values are none (no encryption), aes256, aes192,
              aes128 and 3des.  Enabling crypto_cipher, requires also enabling
              of crypto_hash.

              The default is aes256.

- Corosync/Totem messages are not encrypted with the following configurations in /etc/corosync/corosync.conf ::
    
    crypto_hash: none
    crypto_cipher: none
