# DataCSV: Store and Restore Data Using CSV.jl

## Introduction

### What for:
Gigantic amount of data items can be stored and extracted using Dictionary-like CSV files without exerting the memory.

### Features:
- New data is appended to the csv file in disk without risk of lost in case of shutdown.
- A column named "Data" is preserved to restore the data which is converted into String.
- All other columns are keys for query.
- A Dict data can be directly converted into a csv item.

##  Usage

Exported Functions:

- CSVInfo(::Dict, String)::CSVInfo  Returns the object containing the information of the current csv file. 

```julia
sample = ( x = 5, y = 3 )
info = CSVInfo( sample, "./data.csv" )
```
- dict2File(::Dict, ::Any, ::CSVInfo)::Unit Writes item to the csv file.

```julia
for  x in 1:20
    for y in 1:10
        matrix = rand( x % 3 + 1, y % 4 + 1 )
        avg = sqrt( 0.5 * ( x ^ 2 + y ^ 2))
        dict2File( ( x = x, y = y ), Dict( :matrix  => matrix, :avg => avg ), info )
        print( " Item x = $x, y = $y  and its data is safely stored in CSV    \r" )
    end
end
```

- file2Row( ::CSVInfo )::CSV.Rows2 Find the row iterator of the csv file.

```julia
rows = file2Rows( info ) # rows is a row iterator 
```
- findRows( filter::Function, ::CSV.Rows2; iter = false , dmapper = identity, nokeys = true)
Get the row iterator from rows where filter returns true:

```julia

# Get all matrices of size 2 x 3

# Return a iterator, not a Array -> iter
# Discard all key columns other than the :Data column -> nokeys
# Only return the [:matrix] from the Dict. -> dmapper

mx23 = findRows( r -> r.x % 3 == 1 && r.y % 4 == 2, rows; iter = true, nokeys = true, dmapper = d -> d[:matrix] )
# mx23 can be folded, reduced, aggregated, accumulated, etc.

```

## See Also:
```
/test/runtest.jl
```