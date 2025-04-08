# YARA COMMANDS

## Basic Command
- **Run a YARA rule:**

```bash
yara myrule.yar somefile
```

- **Where:**
  - `myrule.yar` is the YARA rule file
  - `somefile` is the file or directory to be scanned.

## Writing a Simple Rule

```bash
rule examplerule {
    condition: true
}
```

## Example Rule for String Matching

```yaml
rule helloworld_checker {
    strings:
        $hello_world = "Hello World!"
    condition:
        $hello_world
}
```

## Using Multiple Strings in a Condition

```yaml

rule helloworld_checker {
    strings:
        $hello_world = "Hello World!"
        $hello_world_lowercase = "hello world"
        $hello_world_uppercase = "HELLO WORLD"
    condition:
        any of them
}
```

## Condition Operators

- **Operators:** `<=`, `>=`, `!=`

```yaml
rule helloworld_checker {
    strings:
        $hello_world = "Hello World!"
    condition:
        $hello_world <= 10
}

```

  

## Using Keywords in Conditions

```yaml
rule helloworld_checker {
    strings:
        $hello_world = "Hello World!"
    condition:
        $hello_world and filesize < 10KB
}

```


## Loki Tool Command

- **Install Loki and run scans:**

```bash
git clone https://github.com/Neo23x0/Loki.git
cd Loki
./Loki -r myrule.yar --path /path/to/scan
```

## yarGen Tool Command
- **Generate a YARA rule for a suspicious file:**

```bash
python3 yarGen.py -m /path/to/suspicious-file --excludegood -o /path/to/output/file.yar
```

## yarAnalyzer Tool Command
- **Use yarAnalyzer for rule analysis:**

```bash
python3 yarAnalyzer.py --rule myrule.yar --analyze
```

## Fenrir Tool Command
- **Install Fenrir:**

```bash
git clone https://github.com/Neo23x0/Fenrir.git
cd Fenrir
./fenrir.sh -m /path/to/scan
```

## YAYA Tool Command
- **Install YAYA and manage rules:**
```bash
git clone https://github.com/EFForg/yaya.git
cd yaya
python3 yaya.py --import /path/to/rules
```




# YARA COURS

Yara can identify <span style="color:rgb(146, 208, 80)">information based on both binary and textual patterns</span>, such as hexadecimal and strings contained within a file.
Rules are used to label these patterns. These YARA rules are used to determine if the file is malicious or not.

==Example:==

Malware, just like a "Hello World" application, uses strings to store textual data. Here are a few examples of the data that various malware types store within strings:


![[Pasted image 20240710232534.png]]

## My First YARA Rule
The proprietary language that Yara uses for rules is fairly trivial to pick up, but hard to master.
Using a Yara rule is simple. Every `yara` command requires two arguments to be valid, these are:
1. The rule file we create.  (myrule.yar)
2. Name of file, directory, or process ID to use the rule for. (somedirectory)

Ex: yara myrule.yar somedirectory

> [!note] Every rule must have a name and condition. #note 

### STEPS
1. Make a file named "**somefile**" via `touch somefile`
2. Create a new file and name it "**myfirstrule.yar**" like below:
3. Open the "myfirstrule.yar" using a text editor such as `nano` and input the snippet below and save the file:

```shell-session
rule examplerule {
condition: true
}
```

The **name** of the rule - `examplerule`
The **condition** - `condition`.
![[Pasted image 20240710234710.png]]
i) In the first command, the file "somefile" exists.
ii) In the second command, the file "sometextfile" does not exist.
when the condition met, yara prints the "rule name" & "file name"
# YARA Rules
[Writing Yara Rules](https://yara.readthedocs.io/en/stable/writingrules.html)

Some Conditions below:

1. Meta
2. Strings
3. Conditions
4. Keywords

#### Meta
Meta condition is like metadata for the yara rule. We can use `desc`, short for description. It's like commenting code, to summarize the rule.


#### Strings

We can use strings to search for specific text or hexadecimal in files or programs. ==For example,== say we wanted to search a directory for all files containing "Hello World!", we would create a rule such as below:

```yaml
rule helloworld_checker{
strings:
$hello_world = "Hello World!"
condition:
$hello_world
}
```

- Stored inside the variable $hello_world.
- Condition to check the value in the variable exists.

  

==Essentially, if any file has the string "Hello World!" then the rule will match. However, this is literally saying that it will only match if "Hello World!" is found and will not match if "_hello world_" or "_HELLO WORLD_."==

To solve this, the condition `any of them` allows multiple strings to be searched for, like below:

```yaml
rule helloworld_checker{
strings:
$hello_world = "Hello World!"
$hello_world_lowercase = "hello world"
$hello_world_uppercase = "HELLO WORLD"
condition:
any of them
}
```


#### Conditions

Some Conditions Operators are:

1. **<=** less than or equal to
2. **>=** more than or equal to
3. **!=** not equal to

```yaml
rule helloworld_checker{
strings:
$hello_world = "Hello World!"
condition:
$hello_world <= 10
}

```

The rule will now:
1. Look for the "Hello World!" string
2. Only say the rule matches if there are <span style="color:rgb(146, 208, 80)">less than or equal to ten occurrences</span> of the "Hello World!" string
#### Keywords

Some Keywords are:
1. **and**
2. **not**
3. **or**
Checking files which are **less than** <10 kb and has "Hello World!"

```yaml
rule helloworld_checker{
strings:
$hello_world = "Hello World!"
condition:
$hello_world and filesize < 10KB
}
```


### Anatomy of YARA Rule
![[Anatomyofyararule.png]]
## YARA Modules
1. Cuckoo Sandbox
2. Python PE
#### Cuckoo

Cuckoo Sandbox is an automated malware analysis environment. This module allows you to generate Yara rules based upon the behaviours discovered from Cuckoo Sandbox. As this environment executes malware, you can create rules on specific behaviours such as runtime strings and the like.
#### Python PE
Python's PE module allows you to create Yara rules from the various sections and elements of the Windows Portable Executable (PE) structure.

This structure is the standard formatting of all executables and DLL files on windows. Including the programming libraries that are used.

Examining a PE file's contents is an essential technique in malware analysis; this is because behaviours such as cryptography or worming can be largely identified without reverse engineering or execution of the sample.
# YARA Tools

## LOKI


LOKI is a free open-source IOC (_Indicator of Compromise_) scanner. Based on the GitHub page, detection is based on 4 methods: #tool
1. File Name IOC Check
2. Yara Rule Check **(we are here)**
3. Hash Check
4. C2 Back Connect Check

Link: https://github.com/Neo23x0/Loki/releases

## THOR
Multi-platform IOC AND YARA scanner. #tool
Link: https://www.nextron-systems.com/thor-lite/
## FENRIR

This is the 3rd [tool](https://github.com/Neo23x0/Fenrir)  #tool created by Neo23x0 (Florian Roth). The previous 2 are named above. The updated version was created to address the issue from its predecessors, where requirements must be met for them to function. Fenrir is a bash script; it will run on any system capable of running bash (nowadays even Windows).
## YAYA (_Yet Another Yara Automaton_)

YAYA #tool was created by the [EFF](https://www.eff.org/deeplinks/2020/09/introducing-yaya-new-threat-hunting-tool-eff-threat-lab) (_Electronic Frontier Foundation_). "_YAYA is a new open-source tool to help researchers manage multiple YARA rule repositories. YAYA starts by importing a set of high-quality YARA rules and then lets researchers add their own rules, disable specific rulesets, and run scans of files._"
# LOKI


> [!NOTE] Note #note

> As a security analyst, you may need to research various threat intelligence reports, blog postings, etc. and gather information on the latest tactics and techniques used in the wild, past or present. Typically in these readings, IOCs (hashes, IP addresses, domain names, etc.) will be shared so rules can be created to detect these threats in your environment, along with Yara rules.

On the flip side, you might find yourself in a situation where you've encountered something unknown, that your security stack of tools can't/didn't detect. Using tools such as Loki, <span style="color:rgb(221, 85, 85)">you will need to add your own rules based on your threat intelligence gathers</span> or findings from an incident response engagement (forensics).

==Loki already has a set of Yara rules that we can benefit from and start scanning for evil on the endpoint straightaway.==


![[Pasted image 20240714002335.png]]

# yarGen


Some of the files might not get detected by Loki. So Sometimes We need to create a Yara rule to detect this specific file in our environment.
We can manually open the file and attempt to sift through lines upon lines of code to find possible strings that can be used in our newly created Yara rule.

> [!tool] wc #tool

> Find the lines of a particular file: `wc -l file`

[yarGen](https://github.com/Neo23x0/yarGen) is a generator for YARA rules.

> [!tool] yarGen #tool

> "_The main principle is the creation of yara rules from strings found in malware files while removing all strings that also appear in goodware files. Therefore yarGen includes a big goodware strings and opcode database as ZIP archives that have to be extracted before the first use_."

To use yarGen to generate a Yara rule for file 2, you can run the following command:

`python3 yarGen.py -m /home/cmnatic/suspicious-files/file2 --excludegood -o /home/cmnatic/suspicious-files/file2.yar`

A brief explanation of the parameters above:
- `-m` is the path to the files you want to generate rules for
- `--excludegood` force to exclude all goodware strings (_these are strings found in legitimate software and can increase false positives_)
- `-o` location & name you want to output the Yara rule
Another tool created to assist with this is called [yarAnalyzer](https://github.com/Neo23x0/yarAnalyzer/) #tool created by Florian Roth
**Further Reading on creating Yara rules and using yarGen**
- [https://www.bsk-consulting.de/2015/02/16/write-simple-sound-yara-rules/](https://www.bsk-consulting.de/2015/02/16/write-simple-sound-yara-rules/)
- [https://www.bsk-consulting.de/2015/10/17/how-to-write-simple-but-sound-yara-rules-part-2/](https://www.bsk-consulting.de/2015/10/17/how-to-write-simple-but-sound-yara-rules-part-2/)
- [https://www.bsk-consulting.de/2016/04/15/how-to-write-simple-but-sound-yara-rules-part-3/](https://www.bsk-consulting.de/2016/04/15/how-to-write-simple-but-sound-yara-rules-part-3/)

# Valhalla
_[Valhalla](https://valhalla.nextron-systems.com/) boosts your detection capabilities with the power of thousands of hand-crafted high-quality YARA rules._

We should denote that we can conduct searches based on a keyword, tag, ATT&CK technique, sha256, or rule name.

![[Pasted image 20240714005832.png]]


We are provided with the name of the rule, a brief description, a reference link for more information about the rule, along with the rule date.
Some security professionals may not know how to code/script/read code. So In such situation, We can use Valhalla for some threat intelligence gathering to conduct further research regarding these files to receive approval to eradicate these files from the network.

==We can use Valhalla and VT to get most of the information of the specific file==

## Useful Links:
- [YARA Official Documentation](https://yara.readthedocs.io/en/stable/)
- [yarGen Tool](https://github.com/Neo23x0/yarGen)
- [yarAnalyzer Tool](https://github.com/Neo23x0/yarAnalyzer)
- [LOKI IOC Scanner](https://github.com/Neo23x0/Loki/releases)
- [THOR IOC and YARA Scanner](https://www.nextron-systems.com/thor-lite/)
- [FENRIR Tool](https://github.com/Neo23x0/Fenrir)
- [YAYA Tool by EFF](https://www.eff.org/deeplinks/2020/09/introducing-yaya-new-threat-hunting-tool-eff-threat-lab)
- [Valhalla](https://valhalla.nextron-systems.com/)