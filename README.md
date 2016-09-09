# NAME

yargen - generate a rule in yara format with a description of vulnerability in a perl module.
The rule is for Positive Technologies(c) [Approof tool](http://approof.ptsecurity.ru/).

# SYNOPSIS

    yargen --module <Perl::Module> --cve <CVE-####-####>
    Options:
      --module          perl module
      --cve             cve id
      --help            help message
      --license         print license

# OPTIONS

- **--module**  
    Perl module name. Example: [HTML::Scrubber](https://metacpan.org/pod/HTML::Scrubber).

- **--cve**  
    CVE id. Example: [CVE-2015-5667](http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2015-5667).

- **--license**  
    Print license.

- **--help**  
    Print this message.

# DESCRIPTION

**This program** will fetch the description of CVE from <http://cve.mitre.org/>,
<http://vuldb.com> and get an infomation about a perl module from
<http://metacpan.org>.
According to the information from metacpan a list of **all** module releases
will be formed and searched against version identifiers.
Finally a rule in [yara format](http://virustotal.github.io/yara/) with CVE description
will be generated.

Mojolicious module required. Can be installed with one of the following commands:

    cpan Mojolicious
    cpanm --installdeps .
    dnf install perl-Mojolicious


# EXAMPLE

    $ ./yargen --module HTML::Scrubber --cve CVE-2015-5667

    private rule PerlHTMLScrubberModule
    {
    #This rule was generated by: ./yargen --module HTML::Scrubber --cve CVE-2015-5667
           meta:
                   custom_description = "Private rule for identifying Perl HTML::Scrubber Module"
           strings:
                   $package = /package\s+HTML::Scrubber;/
           condition:
                   $package
    }

    rule CVE_2015_5667
    {
    #This rule was generated by: ./yargen --module HTML::Scrubber --cve CVE-2015-5667
           meta:
                   component_name = "HTML-Scrubber module for Perl"
                   component_version = "before 0.15"
                   custom_title = "HTML-Scrubber Module up to 0.14 on Perl Comment Handler cross site scripting"
                   custom_level = "low"
                   custom_description = "<p>Cross-site scripting (XSS) vulnerability in the HTML-Scrubber module before 0.15 for Perl, when the comment feature is enabled, allows remote attackers to inject arbitrary web script or HTML via a crafted comment.</p>"
           strings:
    # !!! Please, edit generated list of versions manually !!!
                   v_0_15 = 'our\s+\$VERSION\s*=\s*(\'|\")0.15(\'|\");'
                   v_0_14 = 'our\s+\$VERSION\s*=\s*(\'|\")0.14(\'|\");'
                   v_0_13 = 'our\s+\$VERSION\s*=\s*(\'|\")0.13(\'|\");'
                   v_0_12 = 'our\s+\$VERSION\s*=\s*(\'|\")0.12(\'|\");'
                   v_0_11 = 'our\s+\$VERSION\s*=\s*(\'|\")0.11(\'|\");'
                   v_0_10 = 'our\s+\$VERSION\s*=\s*(\'|\")0.10(\'|\");'
                   v_0_09 = '\$HTML::Scrubber::VERSION\s*=\s*(\'|\")0.09(\'|\");'
    # !!! Please, edit generated list of versions manually !!!

           condition:
                   PerlHTMLScrubberModule and any of ($v*)
    }
