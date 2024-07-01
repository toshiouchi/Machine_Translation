Machine training data for Mask-Predict machine translation.
Machine training data for a model that upsamples EncoderOuts and uses it as target input of Decoder to calculate CTCLoss.

## Introduction

This report reports on training data for machine translation that translates Japanese to English.The report covers the following four areas.

1.Autoregressive learning using Transformer
  Program file name Autoregressive.ipynb
2.Mask-Predict non-autoregressive learning
  Program file name Mask_Predict.ipynb
3.Non-autoregressive learning with CTCLoss and Mask-Predict
  Program file name CTC_Mask_Predict.ipynb
4.Upsampling the Encoder output to the Decoder target input. loss is CTC.
  Program file name EncoderOuts_Upsampling.ipynb

## Learning Data

The data is from the Japanese-English corpus.

https://www.kecl.ntt.co.jp/icl/lirg/jparacrawl/

This is the data. The train is 1 million, the val is 5000, and the test is 50. In my environment, if an English sentence contains multi-byte characters, the separated English characters in the idx_to_word.json file become garbled, so I did not use English sentences that contained multi-byte characters for learning.

## Autoregressive learning

Learning was performed for 20 epochs with 1 million train data items and 5,000 validation data items. Post a graph of loss and WER changes.

![fig1](https://github.com/toshiouchi/Machine_Translation/assets/121741811/dc68910a-2759-4607-90c1-6847d18d1bfa)

![fig2](https://github.com/toshiouchi/Machine_Translation/assets/121741811/e113eeed-a83b-4bff-a782-3c2ed87a0063)

![fig3](https://github.com/toshiouchi/Machine_Translation/assets/121741811/6ba38474-d5fe-4a68-a927-02889a7bb469)

During this training, we measured WER and some BLEU values. The values ​​in the graph above are obtained by inputting target[:,1:] to the target input of the Transformer Decoder, but for WER and BLEU in the table below, only the source language is input to the model. No training data is used for the target input of the Transformer Decoder. These are numbers obtained by using the model in an autoregressive manner.

<table>
<caption> Autoregressive train data
<thread>
<th>epoch<th>WER<th>BLEU
<tbody>
<tr><td style="text-align:right;"> 1<td>74.0<td>Not recorded
<tr><td style="text-align:right;"> 2<td>72.0<td>Not recorded
<tr><td style="text-align:right;"> 3<td>58.3<td>Not recorded
<tr><td style="text-align:right;"> 4<td>54.3<td>Not recorded
<tr><td style="text-align:right;"> 5<td>47.9<td>Not recorded
<tr><td style="text-align:right;"> 6<td>43.7<td>Not recorded
<tr><td style="text-align:right;"> 7<td>45.8<td>Not recorded
<tr><td style="text-align:right;"> 8<td>47.3<td>Not recorded
<tr><td style="text-align:right;"> 9<td>43.0<td>Not recorded
<tr><td style="text-align:right;">10<td>40.5<td>Not recorded
<tr><td style="text-align:right;">11<td colspan="2">Not measured
<tr><td style="text-align:right;">12<td>36.7<td>Not recorded
<tr><td style="text-align:right;">13<td>35.7<td>41.4
<tr><td style="text-align:right;">14<td>34.5<td>42.1
<tr><td style="text-align:right;">15<td>33.8<td>41.7
<tr><td style="text-align:right;">16<td>32.2<td>45.1
<tr><td style="text-align:right;">17<td>30.7<td>47.4
<tr><td style="text-align:right;">18<td colspan="2">Not measured
<tr><td style="text-align:right;">19<td>29.5<td>48.7
<tr><td style="text-align:right;">20<td>29.1<td>47.9
</table>
</center>
