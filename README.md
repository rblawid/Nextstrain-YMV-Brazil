nextstrain shell .

augur index \
  --sequences data/sequences.fasta \
  --output results/sequence_index.tsv

augur filter \
  --sequences data/sequences.fasta \
  --sequence-index results/sequence_index.tsv \
  --metadata data/metadata.tsv \
  --output-sequences results/filtered.fasta \
  --group-by country year  \
  --sequences-per-group 20


augur align \
--sequences results/filtered.fasta \
--reference-sequence config/ymv_outgroup.gb \
--output results/aligned.fasta \
--fill-gaps


augur tree \
  --alignment results/aligned.fasta \
  --output results/tree_raw.nwk


augur refine \
  --tree results/tree_raw.nwk \
  --alignment results/aligned.fasta \
  --metadata data/metadata.tsv \
  --output-tree results/tree.nwk \
  --output-node-data results/branch_lengths.json \
  --timetree \
  --coalescent opt \
  --date-confidence \
  --date-inference marginal \
  --clock-filter-iqd 3 \
  --stochastic-resolve \
  --clock-rate 1.76e-3 --clock-std-dev 5e-4


augur traits \
  --tree results/tree.nwk \
  --metadata data/metadata.tsv \
  --output-node-data results/traits.json \
  --columns region country date acession host \
  --confidence


augur ancestral \
  --tree results/tree.nwk \
  --alignment results/aligned.fasta \
  --output-node-data results/nt_muts.json \
  --inference joint


augur translate \
  --tree results/tree.nwk \
  --ancestral-sequences results/nt_muts.json \
  --reference-sequence config/ymv_outgroup.gb \
  --output-node-data results/aa_muts.json


augur export v2 \
  --tree results/tree.nwk \
  --metadata data/metadata.tsv \
  --node-data results/branch_lengths.json \
              results/traits.json \
              results/nt_muts.json \
              results/aa_muts.json \
  --colors config/colors.tsv \
  --lat-longs config/lat_longs.tsv \
  --auspice-config config/auspice_config.json \
  --output auspice/ymv.json


nextstrain view auspice/

