# lua-resty-passwdqc

LuaJIT FFI bindings to [libpasswdqc](http://www.openwall.com/passwdqc/) — a password / passphrase strength checking and policy enforcement toolset.

## Synopsis

```lua
local password = require "resty.passwdqc"

-- Generate a random password with default settings
local pw, err = password.random()
if pw then
  print("01. success: ", pw)
else
  print("01. error: ", err)
end

-- Generate a random password with random=24
local pw, err = password.random(24)
if pw then
  print("02. success: ", pw)
else
  print("02. error: ", err)
end

-- Generate a random password with random=85
local pw, err = password.random(85)
if pw then
  print("03. success: ", pw)
else
  print("03. error: ", err)
end

-- Generate a random password with random=10
local pw, err = password.random(10)
if pw then
  print("04. success: ", pw)
else
  print("04. error: ", err)
end

-- You can also supply the options as a table
-- Generate a random password with random=85
local pw, err = password.random({ random = 64 })
if pw then
  print("05. success: ", pw)
else
  print("05. error: ", err)
end

-- Check if password strength is enough using default settings
local ok, err = password.check('newpassword', 'oldpassword')
if ok then
  print("06. success", "new password is fine")
else
  print("06. error: ", err)
end

-- Check if password strength is enough using default settings
local ok, err = password.check('new2!password', 'oldpassword')
if ok then
  print("07. success: ", "new password is fine")
else
  print("07. error: ", err)
end

-- Check if password strength is enough using default settings
local ok, err = password.check('new2!password', 'not_based_on_this')
if ok then
  print("08. success: ", "new password is fine")
else
  print("08. error: ", err)
end

-- Check that password is not in denylist
local ok, err = password.check('new2!password', {
  -- file "DENYLIST" containing "new2!password"
  denylist = "DENYLIST"
})
if ok then
  print("09. success: ", "new password is fine")
else
  print("09. error: ", err)
end

-- Check that password is not based on a word list entry
local ok, err = password.check('doge2!cat', {
  -- file "WORDLIST" containing "doge2!cat"
  wordlist = "WORDLIST"
})
if ok then
  print("10. success: ", "new password is fine")
else
  print("10. error: ", err)
end

-- Check that password does not appear in a database (filter)
local ok, err = password.check('this2is!not4good', {
  -- file "FILTER" created with:
  -- echo 'this2is!not4good' | pwqfilter --create=1 -o FILTER
  filter = "FILTER"
})
if ok then
  print("11. success: ", "new password is fine")
else
  print("11. error: ", err)
end

-- You may also supply settings to check, the defaults are:
--
-- {
--     min        = "disabled,24,11,8,7",
--     max        = 72,
--     passphrase = 3,
--     match      = 4,
--     similar    = "deny",
--     random     = 47
--     wordlist   = nil, -- a string pointing to a file path
--     denylist   = nil, -- a string pointing to a file path
--     filter     = nil, -- a string pointing to a file path
-- }
--
-- Read more from here: http://www.openwall.com/passwdqc/README.shtml

local ok, err = password.check('newpass', nil, { min = "disabled,24,11,9,8", match = 3 })
if ok then
  print("12. success: ", "new password is fine")
else
  print("12. error: ", err)
end

-- You don't need to supply old pass as nil, so the above is same as:
local ok, err = password.check('newpass', { min = "disabled,24,11,9,8", match = 3 })
if ok then
  print("13. success: ", "new password is fine")
else
  print("13. error: ", err)
end

-- If you need to run more tests with same settings, you can use:
local quality = password.new({ min = "disabled,24,11,9,8", match = 3 })
local ok, err = quality:check("This!Is2Awesom3!", "IUsedToUse7his")
if ok then
  print("14. success: ", "new password is fine")
else
  print("14. error: ", err)
end
```

The above would output something similar to this:

```
01. success: Friend-Vanity3elder
02. success: raid-pulpit
03. success: Stark=mast8air*Fairly$Null9
04. error: Error parsing parameter "random=10": Invalid parameter value
05. success: Mock-now-Sunny5korea
06. error: not enough different characters or classes for this length
07. error: is based on the old one
08. success: new password is fine
09. error: is in deny list
10. error: based on a word list entry
11. error: appears to be in a database
12. error: too short
13. error: too short
14. success: new password is fine
```

## License

`lua-resty-passwdqc` uses two clause BSD license.

```
Copyright (c) 2016 - 2024 Aapo Talvensaari
All rights reserved.

Redistribution and use in source and binary forms, with or without modification,
are permitted provided that the following conditions are met:

* Redistributions of source code must retain the above copyright notice, this
  list of conditions and the following disclaimer.

* Redistributions in binary form must reproduce the above copyright notice, this
  list of conditions and the following disclaimer in the documentation and/or
  other materials provided with the distribution.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND
ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED
WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE
DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE FOR
ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES
```
