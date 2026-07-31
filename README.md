# AFFINITY-ANSWERS-
Sql Queries :
a) This query counts the total number of Acacia plant entries available in the taxonomy table.

SELECT COUNT(*) AS acacia_types
COUNT(*) counts all rows that satisfy the given condition.
AS acacia_types gives the output column a meaningful name.
FROM taxonomy
Specifies that the data will be retrieved from the taxonomy table, which stores NCBI taxonomy information.
WHERE species LIKE 'Acacia%'
Filters rows where the species name starts with "Acacia".
% is a wildcard representing any number of characters.
OR tax_string LIKE '%Acacia%'
Also checks whether the taxonomy hierarchy (tax_string) contains the word "Acacia".
This ensures that Acacia entries are found even if the species name itself is stored differently.

b) This query identifies the wheat species (Triticum) that has the longest DNA sequence.
Retrieves:
t.species – Wheat species name.
r.rfamseq_acc – DNA sequence accession number.
r.length – Length of the DNA sequence.
FROM rfamseq AS r
Uses the rfamseq table, which stores DNA/RNA sequence information.
JOIN taxonomy AS t
ON r.ncbi_id = t.ncbi_id
Joins the rfamseq and taxonomy tables using the common NCBI Taxonomy ID.
This allows the sequence information to be linked with the corresponding species.
WHERE
WHERE t.species LIKE '%Triticum%'
   OR t.tax_string LIKE '%Triticum%'
Filters the records to include only wheat species belonging to the Triticum genus.
ORDER BY
ORDER BY r.length DESC
Sorts all wheat DNA sequences from the longest to the shortest.
LIMIT 1
Returns only the first record, which is the wheat species having the longest DNA sequence.

c) This query retrieves the family accession, family name, and the maximum DNA sequence length for each family.
Only families with a maximum sequence length greater than 1,000,000 are included.
The results are sorted in descending order of sequence length and paginated to return the 9th page, with 15 records per page.
rfam_acc – Unique accession ID of the Rfam family.
rfam_id – Name/identifier of the Rfam family.
MAX(rs.length) – Longest DNA sequence associated with that family.
FROM family
Starts from the family table, which contains information about all Rfam families.
First JOIN
JOIN full_region AS fr
ON f.rfam_acc = fr.rfam_acc
Links each family with all regions where that family occurs.
Second JOIN
JOIN rfamseq AS rs
ON fr.rfamseq_acc = rs.rfamseq_acc
Retrieves the corresponding DNA sequence information for each family.
GROUP BY
GROUP BY
    f.rfam_acc,
    f.rfam_id
Groups all sequences belonging to the same family so aggregate functions such as MAX() can be applied.
HAVING
HAVING MAX(rs.length) > 1000000
Filters grouped results to include only families whose longest DNA sequence exceeds 1,000,000 bases.
HAVING is used because MAX() is an aggregate function. Unlike WHERE, HAVING filters the results after grouping.
ORDER BY
ORDER BY
    max_length DESC
Sorts the families from the longest DNA sequence to the shortest.
Pagination
LIMIT 15 OFFSET 120;
LIMIT 15 returns 15 records per page.
OFFSET 120 skips the first 120 records.
The offset is calculated as:
Offset = (Page Number − 1) × Records Per Page
       = (9 − 1) × 15
       = 120
Therefore, the query returns records 121–135, which correspond to the 9th page.
