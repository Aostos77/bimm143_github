# Class 6: R functions
Alejandro Ostos (PID: A17978684)

All functions in R have at least 3 things:

- A **name**, we pick this and use it to call the function.
- Input **arguments**, there can be multiple comma separated inputs to
  the function.
- The **body**, lines of R code that do the work of the function.

Our first wee function:

``` r
add <- function(x, y = 1, z = 1){
  (x + y)/z
}
```

Let’s test our function:

``` r
add(10, 1, 34)
```

    [1] 0.3235294

## A second function

Let’s try something more interesting. Make a sequence generation tool.

The `sample()` function could be useful here.

``` r
sample(1:10, 3)
```

    [1] 10  4  1

change this to work with the nucleotides A, C, G and T. Return 3 of
them.

``` r
n <- c("A", "C", "G", "T")
sample(n, 5, T)
```

    [1] "A" "G" "T" "C" "G"

Turn this snipet into a function that returns a user specified length
DNA sequence. Let’s call it `generate_dna()`…

``` r
generate_dna <- function(y, fasta = T) {
  output <- sample(c("A", "C", "G", "T"), y, T)
  toutput <- paste(output, collapse = "")
  cat("Well done you!\n")
  if(fasta){
    return(toutput)
  } else{
    return(output)
  }
}

generate_dna(50)
```

    Well done you!

    [1] "CGGCCAGAATACATGGTCCCGGATTAGCTCAAGTAAAAACTTCATAACGA"

``` r
generate_dna(30, F)
```

    Well done you!

     [1] "A" "T" "T" "A" "C" "G" "T" "T" "G" "A" "G" "G" "C" "G" "T" "A" "G" "C" "T"
    [20] "G" "G" "G" "G" "C" "G" "A" "A" "A" "T" "A"

The next code assignes the aminoacid to the corresponding codon (I did
it for fun).

``` r
codon_table <- list(
  "ATA"="I", "ATC"="I", "ATT"="I", "ATG"="M",
  "ACA"="T", "ACC"="T", "ACG"="T", "ACT"="T",
  "AAC"="N", "AAT"="N", "AAA"="K", "AAG"="K",
  "AGC"="S", "AGT"="S", "AGA"="R", "AGG"="R",
  "CTA"="L", "CTC"="L", "CTG"="L", "CTT"="L",
  "CCA"="P", "CCC"="P", "CCG"="P", "CCT"="P",
  "CAC"="H", "CAT"="H", "CAA"="Q", "CAG"="Q",
  "CGA"="R", "CGC"="R", "CGG"="R", "CGT"="R",
  "GTA"="V", "GTC"="V", "GTG"="V", "GTT"="V",
  "GCA"="A", "GCC"="A", "GCG"="A", "GCT"="A",
  "GAC"="D", "GAT"="D", "GAA"="E", "GAG"="E",
  "GGA"="G", "GGC"="G", "GGG"="G", "GGT"="G",
  "TCA"="S", "TCC"="S", "TCG"="S", "TCT"="S",
  "TTC"="F", "TTT"="F", "TTA"="L", "TTG"="L",
  "TAC"="Y", "TAT"="Y", "TAA"="*", "TAG"="*",
  "TGC"="C", "TGT"="C", "TGA"="*", "TGG"="W"
  #Table of aminoacids linked to a single character
)
translate_seq <- function(dna_seq) {
  #The following line determines where each codon starts and ends.
  codons <- substring(dna_seq, seq(1, nchar(dna_seq)-2, 3), seq(3, nchar(dna_seq), 3))
  aa <- sapply(codons, function(codon) codon_table[[toupper(codon)]])
  paste(aa, collapse = "")
}

translate_seq(generate_dna(36))
```

    Well done you!

    [1] "SPVQFRFSHTLN"

The next function gives you each of the names of the aminoacids present

``` r
translate_aminoacids <- function(aa_sequence) {
  # Dictionary: single character into full name
  aa_names <- list(
    "A"="Alanine","R"="Arginine","N"="Asparagine","D"="Aspartic acid",
    "C"="Cysteine","Q"="Glutamine","E"="Glutamic acid","G"="Glycine",
    "H"="Histidine","I"="Isoleucine","L"="Leucine","K"="Lysine",
    "M"="Methionine","F"="Phenylalanine","P"="Proline","S"="Serine",
    "T"="Threonine","W"="Tryptophan","Y"="Tyrosine","V"="Valine",
    "*"="Stop codon"
  )
  
  # Split sequence into individual letters
  letters <- strsplit(toupper(aa_sequence), "")[[1]]
  
  # Translate each letter to its full name
  full_names <- sapply(letters, function(a) aa_names[[a]])
  
  return(full_names)
}
```

Let’s try them all together (for fun again)

``` r
seq1 <- generate_dna(60)
```

    Well done you!

``` r
seq1
```

    [1] "TTGCAACTTCCGGGATAGAGAACGTGGACTTTGAATAGGACTAGATTCCCCTCGAAGGCC"

``` r
ami1 <- translate_seq(seq1)
ami1
```

    [1] "LQLPG*RTWTLNRTRFPSKA"

``` r
translate_aminoacids(ami1)
```

                  L               Q               L               P               G 
          "Leucine"     "Glutamine"       "Leucine"       "Proline"       "Glycine" 
                  *               R               T               W               T 
       "Stop codon"      "Arginine"     "Threonine"    "Tryptophan"     "Threonine" 
                  L               N               R               T               R 
          "Leucine"    "Asparagine"      "Arginine"     "Threonine"      "Arginine" 
                  F               P               S               K               A 
    "Phenylalanine"       "Proline"        "Serine"        "Lysine"       "Alanine" 

## A more advanced example

Make a third function that generates protein sequence of a user
specified length and format.

``` r
generate_prot <- function(x, fasta = T, fullname = F){
    aa_names <- list(
    "A"="Alanine","R"="Arginine","N"="Asparagine","D"="Aspartic acid",
    "C"="Cysteine","Q"="Glutamine","E"="Glutamic acid","G"="Glycine",
    "H"="Histidine","I"="Isoleucine","L"="Leucine","K"="Lysine",
    "M"="Methionine","F"="Phenylalanine","P"="Proline","S"="Serine",
    "T"="Threonine","W"="Tryptophan","Y"="Tyrosine","V"="Valine")
    #Only outputs the symbol of amino acid
    simpleoutput <- sample(names(aa_names), x, T)
    #Renders the full name of amino acid
    fulloutput <- sample(aa_names, x, T)
    
    if(fasta == T & fullname == F){
      paste(simpleoutput, collapse = "")
    } else if(fasta == T & fullname == T){
      paste(fulloutput, collapse = " --> ")
    } else {
      fulloutput
    }
}
```

Try this out

``` r
generate_prot(100)
```

    [1] "KWTVVNWHGGIKCFFTIIAKGHIATNVVNGSDAWTAGPGFRKDYDFGKENVGGYTVVFYMGCDIKASCGDVMFWVARFYVSWLVNGLCQQDPEECRYNHM"

> Q. Generate a random protein sequences between lengths 5 and 12
> amino-acids.

One approach is to do this by brute force calling our function for each
length 5 to 12.

Another approach is to write a `for()` loop to itterate over the input
valued 5 to 12.

A very useful third R specific approach is to use the `sapply()`
function.

``` r
seq_lengths <- 5:12
for (i in seq_lengths){
  cat(">", i, "\n")
  cat(generate_prot(i))
  cat("\n")

}
```

    > 5 
    GSQSI
    > 6 
    CEDEYI
    > 7 
    YLRQDCC
    > 8 
    IHLPPCYI
    > 9 
    EWGDAHLEG
    > 10 
    GIVISPFTGQ
    > 11 
    HAPVDWSGVVH
    > 12 
    FPIWYSSWHSRE

``` r
sapply(5:12, generate_prot)
```

    [1] "HWFAV"        "ATMWPV"       "QKPNDDL"      "IPVKMMMG"     "FFVMETTDT"   
    [6] "PASGWVWQIV"   "VRSKVSVTFQD"  "GVYYGGRTQGIH"

> **Key-Point** : Writing functions in R is doable but not the easiest
> thing. Starting with a working snippet of code and then using LLM
> tools to improve and generalize your function.
