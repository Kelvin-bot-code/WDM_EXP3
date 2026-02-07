### EX3 Implementation of GSP Algorithm In Python
### DATE: 
### AIM: To implement GSP Algorithm In Python.
### Description:
The Generalized Sequential Pattern (GSP) algorithm is a data mining technique used for discovering frequent patterns within a sequence database. It operates by identifying sequences that frequently occur together. GSP works by employing a depth-first search strategy to explore and extract frequent patterns efficiently.
### Steps:
1. <strong>Database Scanning:</strong> GSP scans the sequence database to determine the support of each item in the dataset.
2. <strong>Candidate Generation:</strong> It generates a set of candidate sequences using frequent items found in the previous step.
3. <strong>Pattern Growth:</strong> It extends the candidate sequences by merging them to form longer patterns, checking their support against a user-defined minimum support threshold.
4. <strong>Repeat:</strong> The process continues until no new sequences meet the minimum support threshold.
<p align="justify">
GSP finds application in various domains such as market basket analysis, web usage mining, bioinformatics, and more. For instance, in retail, GSP can identify common purchasing patterns, helping businesses understand customer behavior for targeted marketing or inventory management.
</p>

### Procedure:
<p align="justify">
1. From collections import defaultdict, from itertools import combinations: Imports necessary libraries/modules. defaultdict is
used to create a dictionary with default values and combinations generates all possible combinations of a sequence.</p>
<p align="justify">
2. generate_candidates(dataset, k): Function to generate candidate k-item sequences from a dataset. It loops through each sequence in the
dataset and finds combinations of length k for each sequence, updating their counts in a dictionary.</p>
<p align="justify">
3. gsp(dataset, min_support): Function that implements the Generalized Sequential Pattern (GSP) algorithm. It iterates through increasing
sequence lengths (k) until no new frequent patterns are found. It calls generate_candidates() to find patterns of varying lengths.</p>
<p align="justify">
4. Example dataset for each category: Defines example sequences for top wear, bottom wear, and party wear categories.</p>
<p align="justify">
5. Minimum support threshold: Sets the minimum support count required for a pattern to be considered frequent.</p>
<p align="justify">
6. Perform GSP algorithm for each category: Applies the GSP algorithm for each category using the defined example datasets and the
minimum support threshold.</p>
<p align="justify">
7. Output the frequent sequential patterns for each category: Prints the frequent sequential patterns 
    along with their support counts
for each wear category.</p>
<p align="justify">
8. Visulaize the sequence patterns using matplotlib.
</p>
### Program:

```python
from itertools import combinations
from collections import defaultdict
import pandas as pd

# Function to generate candidate k-item sequences
def generate_candidates(dataset, k, min_support):
    c = defaultdict(int)
    for seq in dataset:
        # Flatten into a list of unique items for the current sequence
        # (Your logic treats the sequence as a 'bag' of items for combination purposes)
        flat_seq = []
        for itemset in seq:
            if isinstance(itemset, str):
                flat_seq.extend(itemset.split(','))   # split commas if string
            else:
                flat_seq.extend(itemset) # extend if list
        
        # Ensure uniqueness per sequence so we count support (document frequency)
        # We sort flat_seq to ensure tuple keys are consistent (e.g., ('a','b') vs ('b','a'))
        unique_items = sorted(list(set(flat_seq))) # remove duplicates to count support correctly? 
        # Note: Your original code used sorted(flat_seq) directly inside combinations
        # Usually support is 1 per customer/sequence.
        
        # Generate combinations of length k
        # Using set() here ensures we don't count the same pattern multiple times for one user
        current_seq_candidates = set(combinations(sorted(flat_seq), k))
        
        for comb in current_seq_candidates:
            c[comb] += 1
            
    # Filter by minimum support
    res = {}
    for item, support in c.items():
        if support >= min_support:
            res[item] = support
    return res

# Function to perform GSP-like algorithm
def gsp(dataset, min_support):
    k = 1
    fp = defaultdict(int)
    
    while True:
        # Generate candidates for length k
        c = generate_candidates(dataset, k, min_support)
        
        # If no candidates found, stop
        if not c:
            break
            
        # Add found patterns to our final list
        fp.update(c)
        k += 1
        
    return fp

# --- Main Execution ---

# Example dataset
dataset = [
    [["a"], ["b"], ["c"], ["b", "e"], ["c", "f"], ["g"], ["a", "b", "e"]],
    [["a"], ["d"], ["b", "c"], ["c"], ["f", "g"], ["c", "h"]],
    [["b"], ["c"], ["a", "d"], ["e"], ["b"], ["f"], ["c", "d", "f", "g", "h"]],
    [["c"], ["e", "c"], ["e", "h"]]
]

# Minimum support threshold
min_support = 3

# Perform GSP algorithm
dataset_result = gsp(dataset, min_support)

# --- Output Formatting ---

print(f"{'='*60}")
print("Frequent Sequential Patterns (Table Format)")
print(f"{'='*60}")

if dataset_result:
    # Prepare data for the table
    table_data = []
    for pattern, support in dataset_result.items():
        table_data.append({
            "Pattern Sequence": str(pattern),
            "Length": len(pattern),
            "Support": support
        })
    
    # Create a DataFrame
    df = pd.DataFrame(table_data)
    
    # Sort by Length (Ascending) and then Support (Descending) for readability
    df = df.sort_values(by=['Length', 'Support'], ascending=[True, False])
    
    # Print using Pandas
    # .to_markdown() requires 'tabulate' package (pip install tabulate)
    # If not present, fallback to standard string output
    try:
        print(df.to_markdown(index=False, numalign="center", stralign="left"))
    except (ImportError, AttributeError):
        print(df.to_string(index=False))
else:
    print("No frequent sequential patterns found in dataset.")
```
### Output:
<img width="537" height="889" alt="image" src="https://github.com/user-attachments/assets/39c37be1-31ea-4534-ac96-6e2bfa15a269" />

### Visualization:
```python
import matplotlib.pyplot as plt

# Updated function to visualize GSP results
def visualize_patterns_line(result, category_name):
    if result:
        # Sorting by support count (optional, but makes the line plot more readable)
        sorted_results = dict(sorted(result.items(), key=lambda item: item[1], reverse=True))
        
        patterns = [", ".join(map(str, p)) for p in sorted_results.keys()]
        support = list(sorted_results.values())

        plt.figure(figsize=(12, 6))
        plt.plot(patterns, support, marker='o', linestyle='-', color='teal', linewidth=2, markersize=8)
        
        plt.xlabel('Frequent Item Sequences', fontsize=12)
        plt.ylabel('Support Count', fontsize=12)
        plt.title(f'Frequent Sequential Patterns: {category_name}', fontsize=14)
        
        # Grid helps track the support levels across patterns
        plt.grid(axis='y', linestyle='--', alpha=0.7)
        plt.xticks(rotation=45, ha='right')
        
        plt.tight_layout()
        plt.show()
    else:
        print(f"No frequent sequential patterns found in {category_name}.")

# Call the function for your dataset results
visualize_patterns_line(dataset_result, 'General Dataset')
```
### Output:
<img width="1396" height="676" alt="image" src="https://github.com/user-attachments/assets/ff26bd06-5701-46f7-a49e-623d51467651" />
## Result:
Thus the implementation of the GSP algorithm in python has been successfully executed.

### Result:
