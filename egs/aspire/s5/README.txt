
This README documents this tar archive that contains some chain models.

you should un-tar this inside the egs/aspire/s5 directory of kaldi;
it relates to things that are produced by those scripts.
This was created around when kaldi's master branch was at git log
503e5ded49281a57c4b60bb53e4787100e35e0ce
which was on Sep 30 2016.

We also ran the following (and you should, too):


### RUN the following command ###
steps/online/nnet3/prepare_online_decoding.sh \
  --mfcc-config conf/mfcc_hires.conf data/lang_chain exp/nnet3/extractor exp/chain/tdnn_7b exp/tdnn_7b_chain_online

### RUN the following command ###
utils/mkgraph.sh --self-loop-scale 1.0 data/lang_pp_test exp/tdnn_7b_chain_online exp/tdnn_7b_chain_online/graph_pp

### run the following command if you need the 4-gram language models (e.g. you
### plan to do lattice rescoring
utils/build_const_arpa_lm.sh \
   data/local/lm/4gram-mincount/lm_unpruned.gz data/lang_pp_test data/lang_pp_test_fg



to create the directory exp/tdnn_7b_chain_online, and then we ran
the following (but don't bother if you don't have the data):

steps/online/nnet3/decode.sh --cmd "$decode_cmd" --nj 25 \
     --acwt 1.0 --post-decode-acwt 10.0 \
      exp/tdnn_7b_chain_online/graph_pp data/dev exp/tdnn_7b_chain_online/decode_dev


Now, you won't be able to do the above if you don't have the ASPIRE dev data, but you
should be able to run similar things on your own data.
Also, look in the files in exp/tdnn_7b_chain_online/decode_dev/log
(which are included in this archive) to see what the individual command lines
look like:

head exp/tdnn_7b_chain_online/decode_dev/log/decode.1.log
# Running on g01
# Started at Fri Sep 30 21:15:06 EDT 2016
# online2-wav-nnet3-latgen-faster --online=true --do-endpointing=false --frame-subsampling-factor=3 --config=exp/tdnn_7b_online/conf/online.conf --max-active=7000 --beam=15.0 --lattice-beam=6.0 --acoustic-scale=1.0 --word-symbol-table=exp/chain/tdnn_7b/graph_pp/words.txt exp/tdnn_7b_online/final.mdl exp/chain/tdnn_7b/graph_pp/HCLG.fst ark:data/dev/split25/1/spk2utt "ark,s,cs:extract-segments scp,p:data/dev/split25/1/wav.scp data/dev/split25/1/segments ark:- |" "ark:|lattice-scale --acoustic-scale=10.0 ark:- ark:- | gzip -c >exp/tdnn_7b_online/decode_dev/lat.1.gz"
online2-wav-nnet3-latgen-faster --online=true --do-endpointing=false --frame-subsampling-factor=3 --config=exp/tdnn_7b_online/conf/online.conf --max-active=7000 --beam=15.0 --lattice-beam=6.0 --acoustic-scale=1.0 --word-symbol-table=exp/chain/tdnn_7b/graph_pp/words.txt exp/tdnn_7b_online/final.mdl exp/chain/tdnn_7b/graph_pp/HCLG.fst ark:data/dev/split25/1/spk2utt 'ark,s,cs:extract-segments scp,p:data/dev/split25/1/wav.scp data/dev/split25/1/segments ark:- |' 'ark:|lattice-scale --acoustic-scale=10.0 ark:- ark:- | gzip -c >exp/tdnn_7b_online/decode_dev/lat.1.gz'
LOG (online2-wav-nnet3-latgen-faster:ComputeDerivedVars():ivector-extractor.cc:183) Computing derived variables for iVector extractor
LOG (online2-wav-nnet3-latgen-faster:ComputeDerivedVars():ivector-extractor.cc:204) Done.
extract-segments scp,p:data/dev/split25/1/wav.scp data/dev/split25/1/segments ark:-
lattice-scale --acoustic-scale=10.0 ark:- ark:-
fe_03_00001-A-000376-000554 and generally prefer
LOG (online2-wav-nnet3-latgen-faster:main():online2-wav-nnet3-latgen-faster.cc:272) Decoded utterance fe_03_00001-A-000376-000554
...


#note on WER:
grep WER exp/tdnn_7b_chain_online/decode_dev/wer* | utils/best_wer.sh
%WER 15.60 [ 6045 / 38753, 908 ins, 1751 del, 3386 sub ] exp/tdnn_7b_chain_online/decode_dev/wer_10

# .. note, data/dev isn't the actual dev data, it's a small amount of data held
# out from training data.
