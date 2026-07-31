# AFFINITY ANSWERS

## SQL Queries and Explanation

a) Count the Number of Acacia Plant Types in the `taxonomy` Table

**SQL Query**
```
SELECT COUNT(*) AS acacia_types
FROM taxonomy
WHERE species LIKE 'Acacia%'
   OR tax_string LIKE '%Acacia%';
```
This query counts the total number of **Acacia** plant entries available in the `taxonomy` table.

* `COUNT(*)` counts all rows that satisfy the specified condition.
* `AS acacia_types` assigns a meaningful alias to the output column.
* `FROM taxonomy` specifies that the data is retrieved from the `taxonomy` table, which stores NCBI taxonomy information.
* `WHERE species LIKE 'Acacia%'` filters records whose species name starts with **Acacia**.
* The `%` symbol is a wildcard that matches any sequence of characters.
* `OR tax_string LIKE '%Acacia%'` additionally checks whether the taxonomy hierarchy contains the word **Acacia**, ensuring that all relevant Acacia entries are included.
b) Find the Wheat Species with the Longest DNA Sequence
**SQL Query**
```
SELECT
    t.species,
    r.rfamseq_acc,
    r.length
FROM rfamseq AS r
JOIN taxonomy AS t
    ON r.ncbi_id = t.ncbi_id
WHERE t.species LIKE '%Triticum%'
   OR t.tax_string LIKE '%Triticum%'
ORDER BY r.length DESC
LIMIT 1;
```
This query identifies the **wheat species (Triticum)** that has the longest DNA sequence.
* `SELECT t.species, r.rfamseq_acc, r.length` retrieves:
  * `t.species` – Wheat species name.
  * `r.rfamseq_acc` – DNA sequence accession number.
  * `r.length` – Length of the DNA sequence.
* `FROM rfamseq AS r` selects data from the `rfamseq` table, which stores DNA/RNA sequence information.
* `JOIN taxonomy AS t ON r.ncbi_id = t.ncbi_id` joins the `rfamseq` and `taxonomy` tables using the common **NCBI Taxonomy ID**, allowing each sequence to be associated with its species.
* `WHERE t.species LIKE '%Triticum%' OR t.tax_string LIKE '%Triticum%'` filters the results to include only wheat species belonging to the **Triticum** genus.
* `ORDER BY r.length DESC` sorts the DNA sequences from longest to shortest.
* `LIMIT 1` returns only the first record, which represents the wheat species with the longest DNA sequence.
---
c) Retrieve the 9th Page of Families with DNA Sequence Length Greater Than 1,000,000
**SQL Query**
```
SELECT
    f.rfam_acc,
    f.rfam_id,
    MAX(rs.length) AS max_length
FROM family AS f
JOIN full_region AS fr
    ON f.rfam_acc = fr.rfam_acc
JOIN rfamseq AS rs
    ON fr.rfamseq_acc = rs.rfamseq_acc
GROUP BY
    f.rfam_acc,
    f.rfam_id
HAVING MAX(rs.length) > 1000000
ORDER BY
    max_length DESC
LIMIT 15 OFFSET 120;
```
This query retrieves the **family accession ID**, **family name**, and the **maximum DNA sequence length** for each family.
* `SELECT f.rfam_acc, f.rfam_id, MAX(rs.length) AS max_length` returns:
  * `rfam_acc` – Unique accession ID of the Rfam family.
  * `rfam_id` – Family name.
  * `MAX(rs.length)` – The longest DNA sequence associated with each family.
* `FROM family AS f` starts with the `family` table, which contains information about all Rfam families.
* `JOIN full_region AS fr ON f.rfam_acc = fr.rfam_acc` links each family with its annotated sequence regions.
* `JOIN rfamseq AS rs ON fr.rfamseq_acc = rs.rfamseq_acc` retrieves the corresponding DNA sequence information.
* `GROUP BY f.rfam_acc, f.rfam_id` groups all sequences belonging to the same family so that aggregate functions such as `MAX()` can be applied.
* `HAVING MAX(rs.length) > 1000000` filters the grouped results to include only families whose maximum DNA sequence length exceeds **1,000,000** bases. `HAVING` is used because `MAX()` is an aggregate function.
* `ORDER BY max_length DESC` sorts the families in descending order based on their maximum DNA sequence length.
* `LIMIT 15 OFFSET 120` implements pagination:
  * `LIMIT 15` returns **15 records** per page.
  * `OFFSET 120` skips the first **120 records**.
The offset is calculated as:
```
Offset = (Page Number − 1) × Records Per Page
       = (9 − 1) × 15
       = 120
```
Therefore, the query returns **records 121–135**, corresponding to the **9th page** of the results.
---
