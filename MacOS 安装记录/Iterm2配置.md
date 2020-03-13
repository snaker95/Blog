# Iterm2 配置

* 把这个配置文件，复制到新电脑的下面目录下：

```jsx
~/Library/Application Support/iTerm2/DynamicProfiles/
```

* 配置文件

```json
{
"Profiles": [
{
  "Working Directory" : "",
  "Prompt Before Closing 2" : false,
  "Selected Text Color" : {
    "Red Component" : 0.76068115234375,
    "Color Space" : "sRGB",
    "Blue Component" : 0.76068115234375,
    "Alpha Component" : 1,
    "Green Component" : 0.76068115234375
  },
  "Rows" : 25,
  "Ansi 11 Color" : {
    "Green Component" : 0.8363599181175232,
    "Red Component" : 0.8774813413619995,
    "Blue Component" : 0.3815441131591797
  },
  "Use Italic Font" : true,
  "HotKey Characters" : "",
  "Foreground Color" : {
    "Green Component" : 0.7714409232139587,
    "Red Component" : 0.7696096301078796,
    "Blue Component" : 0.7099304795265198
  },
  "HotKey Window Floats" : false,
  "Right Option Key Sends" : 0,
  "Character Encoding" : 4,
  "Selection Color" : {
    "Red Component" : 0.39288330078125,
    "Color Space" : "sRGB",
    "Blue Component" : 0.39288330078125,
    "Alpha Component" : 1,
    "Green Component" : 0.39288330078125
  },
  "Triggers" : [
    {
      "action" : "MuteCoprocessTrigger",
      "parameter" : "\/Users\/snaker95\/Develop\/GitHub\/iterm2-zmodem\/iterm2-send-zmodem.sh",
      "regex" : "\\*\\*B0100",
      "partial" : true
    },
    {
      "action" : "MuteCoprocessTrigger",
      "parameter" : " \/Users\/snaker95\/\/Develop\/GitHub\/iterm2-zmodem\/iterm2-recv-zmodem.sh",
      "regex" : "\\*\\*B00000000000000",
      "partial" : true
    }
  ],
  "Blend" : 0.5,
  "Mouse Reporting" : true,
  "Cursor Boost" : 0.0526685393258427,
  "Ansi 4 Color" : {
    "Green Component" : 0.3959920704364777,
    "Red Component" : 0.6146889925003052,
    "Blue Component" : 0.9987757205963135
  },
  "Non-ASCII Anti Aliased" : true,
  "Sync Title" : false,
  "Disable Window Resizing" : false,
  "Description" : "Default",
  "Close Sessions On End" : true,
  "Jobs to Ignore" : [
    "rlogin",
    "ssh",
    "slogin",
    "telnet"
  ],
  "Scrollback With Status Bar" : true,
  "Scrollback Lines" : 0,
  "HotKey Window Reopens On Activation" : true,
  "Hide After Opening" : true,
  "Prevent Opening in a Tab" : false,
  "Flashing Bell" : false,
  "Cursor Guide Color" : {
    "Red Component" : 0.7021318674087524,
    "Color Space" : "sRGB",
    "Blue Component" : 1,
    "Alpha Component" : 0.25,
    "Green Component" : 0.9268307089805603
  },
  "BM Growl" : true,
  "Ansi 3 Color" : {
    "Green Component" : 0.5180569291114807,
    "Red Component" : 0.9809480905532837,
    "Blue Component" : 0.09980705380439758
  },
  "Use Non-ASCII Font" : false,
  "Link Color" : {
    "Red Component" : 0,
    "Color Space" : "sRGB",
    "Blue Component" : 0.734233021736145,
    "Alpha Component" : 1,
    "Green Component" : 0.3591606020927429
  },
  "Shortcut" : "",
  "Background Image Location" : "",
  "Bold Color" : {
    "Green Component" : 0.7714409232139587,
    "Red Component" : 0.7696096301078796,
    "Blue Component" : 0.7099304795265198
  },
  "Use Cursor Guide" : false,
  "Unlimited Scrollback" : true,
  "Custom Command" : "No",
  "HotKey Key Code" : 0,
  "Keyboard Map" : {
    "0xf700-0x260000" : {
      "Action" : 10,
      "Text" : "[1;6A"
    },
    "0x37-0x40000" : {
      "Action" : 11,
      "Text" : "0x1f"
    },
    "0x32-0x40000" : {
      "Action" : 11,
      "Text" : "0x00"
    },
    "0xf709-0x20000" : {
      "Action" : 10,
      "Text" : "[17;2~"
    },
    "0xf70c-0x20000" : {
      "Action" : 10,
      "Text" : "[20;2~"
    },
    "0xf729-0x20000" : {
      "Action" : 10,
      "Text" : "[1;2H"
    },
    "0xf72b-0x40000" : {
      "Action" : 10,
      "Text" : "[1;5F"
    },
    "0xf705-0x20000" : {
      "Action" : 10,
      "Text" : "[1;2Q"
    },
    "0xf703-0x260000" : {
      "Action" : 10,
      "Text" : "[1;6C"
    },
    "0xf700-0x220000" : {
      "Action" : 10,
      "Text" : "[1;2A"
    },
    "0xf701-0x280000" : {
      "Action" : 11,
      "Text" : "0x1b 0x1b 0x5b 0x42"
    },
    "0x38-0x40000" : {
      "Action" : 11,
      "Text" : "0x7f"
    },
    "0x33-0x40000" : {
      "Action" : 11,
      "Text" : "0x1b"
    },
    "0xf703-0x220000" : {
      "Action" : 10,
      "Text" : "[1;2C"
    },
    "0xf701-0x240000" : {
      "Action" : 10,
      "Text" : "[1;5B"
    },
    "0xf70d-0x20000" : {
      "Action" : 10,
      "Text" : "[21;2~"
    },
    "0xf702-0x260000" : {
      "Action" : 10,
      "Text" : "[1;6D"
    },
    "0xf729-0x40000" : {
      "Action" : 10,
      "Text" : "[1;5H"
    },
    "0xf706-0x20000" : {
      "Action" : 10,
      "Text" : "[1;2R"
    },
    "0x34-0x40000" : {
      "Action" : 11,
      "Text" : "0x1c"
    },
    "0xf700-0x280000" : {
      "Action" : 11,
      "Text" : "0x1b 0x1b 0x5b 0x41"
    },
    "0x2d-0x40000" : {
      "Action" : 11,
      "Text" : "0x1f"
    },
    "0xf70e-0x20000" : {
      "Action" : 10,
      "Text" : "[23;2~"
    },
    "0xf702-0x220000" : {
      "Action" : 10,
      "Text" : "[1;2D"
    },
    "0xf703-0x280000" : {
      "Action" : 11,
      "Text" : "0x1b 0x1b 0x5b 0x43"
    },
    "0xf700-0x240000" : {
      "Action" : 10,
      "Text" : "[1;5A"
    },
    "0xf707-0x20000" : {
      "Action" : 10,
      "Text" : "[1;2S"
    },
    "0xf70a-0x20000" : {
      "Action" : 10,
      "Text" : "[18;2~"
    },
    "0x35-0x40000" : {
      "Action" : 11,
      "Text" : "0x1d"
    },
    "0xf70f-0x20000" : {
      "Action" : 10,
      "Text" : "[24;2~"
    },
    "0xf703-0x240000" : {
      "Action" : 10,
      "Text" : "[1;5C"
    },
    "0xf701-0x260000" : {
      "Action" : 10,
      "Text" : "[1;6B"
    },
    "0xf702-0x280000" : {
      "Action" : 11,
      "Text" : "0x1b 0x1b 0x5b 0x44"
    },
    "0xf72b-0x20000" : {
      "Action" : 10,
      "Text" : "[1;2F"
    },
    "0x36-0x40000" : {
      "Action" : 11,
      "Text" : "0x1e"
    },
    "0xf708-0x20000" : {
      "Action" : 10,
      "Text" : "[15;2~"
    },
    "0xf701-0x220000" : {
      "Action" : 10,
      "Text" : "[1;2B"
    },
    "0xf70b-0x20000" : {
      "Action" : 10,
      "Text" : "[19;2~"
    },
    "0xf702-0x240000" : {
      "Action" : 10,
      "Text" : "[1;5D"
    },
    "0xf704-0x20000" : {
      "Action" : 10,
      "Text" : "[1;2P"
    }
  },
  "Ansi 14 Color" : {
    "Green Component" : 0.8197720646858215,
    "Red Component" : 0.3441696763038635,
    "Blue Component" : 0.9206036329269409
  },
  "Ansi 2 Color" : {
    "Green Component" : 0.8792104721069336,
    "Red Component" : 0.5947334170341492,
    "Blue Component" : 0.1400438249111176
  },
  "Background Image Is Tiled" : false,
  "Send Code When Idle" : false,
  "ASCII Anti Aliased" : false,
  "Tags" : [

  ],
  "Ansi 9 Color" : {
    "Green Component" : 0,
    "Red Component" : 0.9568350315093994,
    "Blue Component" : 0.3728577196598053
  },
  "Use Bold Font" : true,
  "Silence Bell" : false,
  "Ansi 12 Color" : {
    "Green Component" : 0.3959920704364777,
    "Red Component" : 0.6146889925003052,
    "Blue Component" : 0.9987757205963135
  },
  "Window Type" : 6,
  "Allow Title Reporting" : true,
  "Use Bright Bold" : true,
  "Has Hotkey" : true,
  "HotKey Modifier Activation" : 0,
  "Cursor Text Color" : {
    "Green Component" : 0.7714409232139587,
    "Red Component" : 0.7696096301078796,
    "Blue Component" : 0.7099304795265198
  },
  "HotKey Window Dock Click Action" : 1,
  "Default Bookmark" : "No",
  "Cursor Color" : {
    "Green Component" : 0.9667491912841797,
    "Red Component" : 0.9655450582504272,
    "Blue Component" : 0.9264779686927795
  },
  "Ansi 1 Color" : {
    "Green Component" : 0,
    "Red Component" : 0.9568350315093994,
    "Blue Component" : 0.3728577196598053
  },
  "Name" : "Hotkey Window",
  "Guid" : "49923214-977B-4111-AD87-AEF9CE841115",
  "Blinking Cursor" : true,
  "Ansi 8 Color" : {
    "Green Component" : 0.3695960938930511,
    "Red Component" : 0.3829454779624939,
    "Blue Component" : 0.2965214252471924
  },
  "Ansi 10 Color" : {
    "Green Component" : 0.8792104721069336,
    "Red Component" : 0.5947334170341492,
    "Blue Component" : 0.1400438249111176
  },
  "Idle Code" : 0,
  "Badge Color" : {
    "Red Component" : 1,
    "Color Space" : "sRGB",
    "Blue Component" : 0,
    "Alpha Component" : 0.5,
    "Green Component" : 0.1491314172744751
  },
  "Ambiguous Double Width" : true,
  "Blur Radius" : 0.1,
  "Ansi 0 Color" : {
    "Green Component" : 0.1005128845572472,
    "Red Component" : 0.1005158945918083,
    "Blue Component" : 0.1005145907402039
  },
  "Cursor Type" : 1,
  "Blur" : true,
  "Normal Font" : "PowerlineExtraSymbols 14",
  "Application Keypad Allowed" : false,
  "Vertical Spacing" : 1,
  "Use Underline Color" : false,
  "Ansi 7 Color" : {
    "Green Component" : 0.7714409232139587,
    "Red Component" : 0.7696096301078796,
    "Blue Component" : 0.7099304795265198
  },
  "Space" : -1,
  "HotKey Window AutoHides" : false,
  "Command" : "",
  "Terminal Type" : "xterm-256color",
  "Horizontal Spacing" : 0.997535062123494,
  "Option Key Sends" : 0,
  "Only The Default BG Color Uses Transparency" : true,
  "Blink Allowed" : true,
  "HotKey Modifier Flags" : 0,
  "Ansi 15 Color" : {
    "Green Component" : 0.9654953479766846,
    "Red Component" : 0.9653725624084473,
    "Blue Component" : 0.9359474778175354
  },
  "Minimum Contrast" : 0.1061123934659091,
  "Unicode Version" : 9,
  "Ansi 6 Color" : {
    "Green Component" : 0.8197720646858215,
    "Red Component" : 0.3441696763038635,
    "Blue Component" : 0.9206036329269409
  },
  "Transparency" : 0.1002042354060914,
  "HotKey Activated By Modifier" : true,
  "Background Color" : {
    "Green Component" : 0.1005128845572472,
    "Red Component" : 0.1005158945918083,
    "Blue Component" : 0.1005145907402039
  },
  "HotKey Window Animates" : true,
  "Open Toolbelt" : false,
  "Screen" : -2,
  "HotKey Characters Ignoring Modifiers" : "",
  "Non Ascii Font" : "AdobeHeitiStd-Regular 14",
  "Ansi 13 Color" : {
    "Green Component" : 0,
    "Red Component" : 0.9568350315093994,
    "Blue Component" : 0.3728577196598053
  },
  "Columns" : 80,
  "HotKey Alternate Shortcuts" : [

  ],
  "Use Tab Color" : false,
  "Visual Bell" : true,
  "Custom Directory" : "No",
  "Ansi 5 Color" : {
    "Green Component" : 0,
    "Red Component" : 0.9568350315093994,
    "Blue Component" : 0.3728577196598053
  },
  "ASCII Ligatures" : false
}
]
}
```