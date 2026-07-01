# Genetic Algorithm for Storage Allocation

A genetic algorithm that packs boxes into warehouse storage zones to maximize how much of
each zone's volume is used, a bin-packing problem framed for real operations. The project
tests which targeted improvements over a standard genetic algorithm actually raise fitness,
and finds that one of them backfires. It includes an IEEE-style paper.

## Authors

A three-person academic project at Columbus State University (TSYS School of Computer
Science): Ashley Bedford, Mayank Gaba, and Nia Foy. The paper was co-authored.

## The problem

Warehouse space is often underused because packing decisions are left to whoever is on the
floor. This project models the decision directly. The storage area is split into three zone
sizes, Small, Medium, and Large, each with its own dimensions and volume. Boxes of a fixed
size per zone must be placed so that as much of each zone's volume is filled as possible. A
candidate packing is a chromosome of bits, one per box, where 1 means the box is placed. The
fitness function is the percentage of a zone's volume the packing fills, so higher is better
and the genetic algorithm evolves packings toward fuller zones over many generations.

## What was tested

The notebook builds a standard genetic algorithm, then changes one thing at a time to see
what actually drives fitness. The four runs are `standard`, `strong_population`,
`strong_parent`, and `strong_parent_population`:

    standard()                   random initialization, random parent selection
    strong_population()          seed the population only with high-fitness packings
    strong_parent()              select parents by fitness, with one-point crossover
    strong_parent_population()   both improvements together

Operators are shared across runs: one-point crossover and bit-flip mutation, gated by a
crossover rate and a reproduction rate, with the strong-initialization variant admitting
only solutions above an 85 percent fitness threshold into the starting population.

## Results

The finding is that parent selection was the lever that mattered, a strong starting
population on its own backfired, and the two together were best.

- **Standard GA** stayed flat and randomized and could not break past roughly 70 percent
  fitness. This is the baseline to beat.
- **Strong population initialization on its own backfired.** Seeding the population only with
  high-fitness packings started strong but decayed from around 90 percent toward 70 percent
  over generations, the opposite of the intended effect, because nothing in the standard
  selection held that quality in place. This is the useful negative result of the study.
- **Strong parent selection with one-point crossover** was the winning change, with nearly
  all zone sizes reaching close to 100 percent fitness.
- **Both improvements together** produced the largest gain over time, building on the strong
  parent-selection result.

The takeaway is that a strong starting population only pays off when paired with
fitness-biased selection to hold and build on it, and that the combined approach fills
nearly all available volume where the standard algorithm leaves a large share empty.

## Running it

The notebook is self-contained and reads no external data, so it runs with no dataset setup.

    pip install -r requirements.txt
    jupyter notebook notebook/storage_allocation_genetic_algorithm.ipynb

Running it reproduces the fitness-over-generations plots for each of the four runs.

## Scope and limits

Fixed box sizes per zone and a single geometry. Boxes are uniform within each zone so
orientation and volume stay tractable, which keeps the problem clean but does not capture
mixed-size real inventory.

Fitness values are the best the search reached, not a proven optimum. A genetic algorithm
finds strong packings, not a guaranteed best packing, so the reported values are search
results rather than bounds.

## Reference

Bedford, A., Gaba, M., and Foy, N. Improving Genetic Algorithms for Product Storage
Allocation. TSYS School of Computer Science, Columbus State University. See `paper/`.
