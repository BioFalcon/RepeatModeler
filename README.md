
```
GitHub Conventions:
   - Master branch is the current release
   - Development branch is not gauranteed to be stable
   - Source releases are avaialable under the GitHub release tab and at 
     http://www.repeatmasker.org
```

RepeatModeler 
=============

RepeatModeler is a de novo transpposable element family identification and 
modeling package. At the heart of RepeatModeler are three de-novo repeat finding
programs ( RECON, RepeatScout and LtrDetector ) which employ complementary 
computational methods for identifying repeat element boundaries and family 
relationships from sequence data.

RepeatModeler assists in automating the runs of the various algorithms
given a genomic database, clustering redundant results, refining and 
classifying the families and producing a high quality library of
transposable element families suitable for use with RepeatMasker and
ultimately for submission to the Dfam.org database.

Authors
-------
 RepeatModeler:
   Robert Hubley, Arian Smit - Institute for Systems Biology

 LTR Pipeline Extensions:
   Jullien M. Flynn, Cedric Feschotte - Cornell University

Installation
------------

 There are two supported paths to installing RepeatModeler on a 
 UNIX-based server. RepeatModeler may be installed from source as
 described in the "Source Distribution Installation" instructions
 below, or using one of our container images ( Docker or Singularity ).
 The containers include RepeatModeler, it's prerequisites and additional
 transposable element analysis tools/utilities used by Dfam. 

Source Distribution Installation
--------------------------------

  Perl
    Available at http://www.perl.org/get.html. Developed and tested
    with version 5.8.8.

  RepeatMasker & Libraries
    Developed and tested with open-4.0.9. The program is available at 
    http://www.repeatmasker.org/RMDownload.html and is distributed with
    Dfam - an open database of transposable element families.

  RECON - De Novo Repeat Finder, Bao Z. and Eddy S.R.
    Developed and tested with our patched version of RECON ( 1.08 ). 
    The 1.08 version fixes problems with running RECON on 64 bit machines and 
    supplies a workaround to a division by zero bug along with some buffer 
    overrun fixes. The program is available at: 
       http://www.repeatmasker.org/RECON-1.08.tar.gz. 
    The original version is available at http://eddylab.org/software/recon/.

  RepeatScout - De Novo Repeat Finder, Price A.L., Jones N.C. and Pevzner P.A.
    Developed and tested with our multiple sequence version of RepeatScout 
    ( 1.0.6 ).  This version is available at 
    http://www.repeatmasker.org/RepeatScout-1.0.6.tar.gz

  TRF - Tandem Repeat Finder, G. Benson et al.
    You can obtain a free copy at http://tandem.bu.edu/trf/trf.html. 
    RepeatModeler was developed using 4.0.9.

  RMBlast - A modified version of NCBI Blast for use with RepeatMasker
    and RepeatModeler.  Precompiled binaries and source can be found at
    http://www.repeatmasker.org/RMBlast.html

  
  Optional. Additional search engine:
  
    ABBlast - Sequence Search Engine, W. Gish et al.
      Developed and tested with 2.0 [04-May-2006]. The latest versions
      of ABBlast may be downloaded from: http://blast.advbiocomp.com/licensing/


  Optional. Required for running LTR structural search pipeline:

    LtrDetector - 
    Ltr_retriever - 
    MAFFT -
    CD-HIT -
    Ninja -


  1. Obtain the source distribution
       - Github : https://github.com/rmhubley/RepeatModeler
         Available by cloning the master branch of the RepeatModeler repository
         ( latest released version ) or by downloading a release from the 
         repository "releases" tab.

       - RepeatMasker Website : http://www.repeatmasker.org/RepeatModeler

  2. Uncompress and expand the distribution archive:

     Typically:   tar -zxvf RepeatModeler-open-#.#.#.tar.gz
                or
                  gunzip RepeatModeler-open-#.#.#.tar.gz
                  tar -xvf RepeatModeler-open-#.#.#.tar

  3. Configure for your site:

     Automatic:
       + Run the "configure" script contained in the RepeatModeler
         distribution as:

               perl ./configure

          to be prompted for each setting.
      or 
       
 
     By Hand:
       1. Edit the configuration file "RepModelConfig.pm"

     Dynamically:
       1. Use the "configuration overrides" command line options
          with the RepeatModeler programs.  

          e.g.  ./RepeatModeler -rscout_dir .. -recon_dir .. 
         
 
Example Run
-----------

In this example we first downloaded elephant sequences
from Genbank ( approx 11MB ) into a file called elephant.fa. 

  1. Create a Database for RepeatModeler

     RepeatModeler uses a ABBlast/WUBlast XDF database or a NCBI 
     DB ( depending on the search engine used ) as input to the
     repeat modeling pipeline.  A utility is provided to assist
     the user in creating a single database from several 
     types of input structures.  

     <RepeatModelerPath>/BuildDatabase -name elephant elephant.fa

     Run "BuildDatabase" without any options in order to see the 
     full documentation on this utility. There are several options
     which make it easier to import multiple sequence files into
     one database.
  
     NOTE: It is a good idea to place your datafiles and run this
           program suite from a local disk rather than over NFS.

  2. Run RepeatModeler
     
     RepeatModeler runs several compute intensive programs on the
     input sequence.  For best results run this on a machine with
     a moderate amount of memory and several processors.  Our typical
     setup was P4 - 4 cpus, 2.4Ghz, 3GB Memory, and Red Hat Linux. 
     
     nohup <RepeatModelerPath>/RepeatModeler -database elephant >& run.out &
     
     The nohup is used on our machines when running long ( > 3-4 hour ) 
     jobs.  The output is saved to a file and the process is backgrounded.
     For typical runtimes ( can be > 2 days with this configuration )
     see the run statistics section of this file.

  3. Interpret the results

     This development version of RepeatModeler produces a voluminous
     amount of output.  The raw output is directed to a working directory
     named RM_<PID>.<DATE> ie. "RM_5098.MonMar141305172005" and remains
     after each run for debugging purposes.  At the completion of the
     run two files are generated:

               <database_name>-families.fa  : Consensus sequences
               <database_name>-families.stk : Seed alignments

     The seed alignment file is in a Dfam compatible Stockholm format and
     may be uploaded to the new open Dfam_consensus database using the 
     util/dfamConsensusTool.pl. 
     See http://www.repeatmasker.org/RepeatModeler/dfamConsensusTool for
     details.

     The fasta format is useful for running quick custom library searches
     using RepeatMasker.  Ie.:

     <RepeatMaskerPath>/RepeatMasker -lib <database_name>-families.fa mySequence.fa 

     Other files produced in the working directory include:

       RM_<PID>.<DATE>/
          round-1/
               sampleDB-#.fa       : The genomic sample used in this round
               sampleDB-#.fa.lfreq : The RepeatScout lmer table
               sampleDB-#.fa.rscons: The RepeatScout generated consensi
               sampleDB-#.fa.rscons.filtered : The simple repeat/low 
                                               complexity filtered
                                               version of *.rscons
               consensi.fa         : The final consensi db for this round
               family-#-cons.html  : A visualization of the model
                                     refinement process.  This can be opened
                                     in web browsers that support zooming.
                                     ( such as firefox ).
                                     This is used to track down problems 
                                     with the Refiner.pl
               index.html          : A HTML index to all the family-#-cons.html
                                     files.
          round-2/
               sampleDB-#.fa       : The genomic sample used in this round
               msps.out            : The output of the sample all-vs-all 
                                     comparison
               summary/            : The RECON output directory
                    eles           : The RECON family output
               consensi.fa         : Same as above
               family-#-cons.html  : Same as above
               index.html          : Same as above
          round-3/
               Same as round-2
           ..
          round-n/


  Recover from a failure

    If for some reason RepeatModeler fails, you may restart an
    analysis starting from the last round it was working on.  The
    -recoverDir [<i>ResultDir</i>] option allows you to specify a
    diretory ( i.e RM_<PID>.<DATE>/ ) where a previous run of
    RepeatModeler was working and it will automatically determine
    how to continue the analysis.


  Please see the RELEASE-NOTES file for more details.


RepeatModeler Statistics
------------------------
Benchmarks and statistics for runs of RepeatModeler on several sample
genomes.

RepeatModeler 1.0.3 ( RECON + RepeatScout ):

            Genome DB    Sample***   Run Time*   Models   Models       % Sample
Genome      Size (bp)    Size (bp)   (hh:mm)     Built    Classified   Masked**
----------  -----------  ----------  ----------  -------  -----------  --------
Marmoset    3.0 Bbp      228 Mbp      55:37        582      564         36.4    
Zebrafinch  1.2 Bbp      222 Mbp      66:29        178       75          8.6    


  *  Analysis run on a 4 processor P4, 2.4Ghz, 3GB RAM, machine
     running Red Hat Linux.

  ** Includes simple repeats and low complexity DNA. Results
     obtained with RepeatMasker open-3.2.5, WUBlast and
     the -lib option.

  *** Sample size does not include 40 Mbp used in the RepeatScout analysis.  
      This 40 Mbp is randomly chosen and may overlap 0-100% of the 
      sample used in the RECON analysis.



RepeatModeler 1.0.2 ( RECON + RepeatScout ):

            Genome DB    Sample***   Run Time*   Models   Models       % Sample
Genome      Size (bp)    Size (bp)   (hh:mm)     Built    Classified   Masked**
----------  -----------  ----------  ----------  -------  -----------  --------
Human HG18     3.1 Bbp      238 Mbp   46:36        614      611         35.66
Platypus       1.9 Bbp      220 Mbp   76:02        540      457         
Zebrafinch     1.3 Bbp      220 Mbp   63:57        233      104          9.41
Sea Urchin     867 Mbp      220 Mbp   40:03       1830      360         33.85
diatom       32,930,227  32,930,227    4:41        128       35          2.86
Rabbit       11,770,949  11,770,949    3:14         83       72         31.30

  *  Analysis run on a 4 processor P4, 2.4Ghz, 3GB RAM, machine
     running Red Hat Linux.

  ** Includes simple repeats and low complexity DNA. Results
     obtained with RepeatMasker open-3.1.9, WUBlast and
     the -lib option.

  *** Sample size does not include 40 Mbp used in the RepeatScout analysis.  
      This 40 Mbp is randomly chosen and may overlap 0-100% of the 
      sample used in the RECON analysis.



RepeatModeler 1.0.0 ( RECON ):

            Genome DB    Sample      Run Time*   Models   Models       % Sample
Genome      Size (bp)    Size (bp)   (hh:mm)     Built    Classified   Masked**
----------  -----------  ----------  ----------  -------  -----------  --------
Opossum     3.5 Billion  319 Mbp     140:52      1137     467          52.55
Armadillo   47,332,136   47,332,136    6:20      121      92           36.07
Platypus    14,768,992   14,768,992    3:46      18       13           40.69
Rabbit      11,770,949   11,770,949    2:17      20       16           28.67
Elephant    11,550,090   11,550,090    1:21      34       28           37.08

  *  Analysis run on a 4 processor P4, 2.4Ghz, 3GB RAM, machine
     running Red Hat Linux.
  ** Includes simple repeats and low complexity DNA. Results
     obtained with RepeatMasker open-3.0.9, WUBlast and
     the -lib option.


Caveats
-------

  o Genomes with numerous short contigs ( Diatom for example )
    will take longer to BLAST than larger genomes with 
    larger contigs.  This is an optimization problem left for 
    future releases. 

  o This program is not parallelized.  It can only run on
    one node.  This is something we are considering for future
    releases.


Credits
-------

Arnie Kas for the work done on the original MultAln.pm.

Andy Siegel for statistics consultations.

Thanks so much to Warren Gish for his invaluable assistance 
and consultation on his ABBlast program suite.

Alkes Price and Pavel Pevzner for assistance with RepeatScout
and hosting my multi-sequence version of RepeatScout.

This work was supported by the NIH ( R44 HG02244-02), 
( RO1 HG002939 ), ( U24 HG010136 ), and the Institute 
for Systems Biology.


License
-------

This work is licensed under the Open Source License v2.1.  
To view a copy of this license, visit 
http://www.opensource.org/licenses/osl-2.1.php or
see the LICENSE file contained in this distribution.


