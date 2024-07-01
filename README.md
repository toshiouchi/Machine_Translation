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

WER 29.1 and BLEU 47.9 are obtained at 20 epochs.

## Learning with Mask-Predict.

Learning was performed for 20 epochs with 1 million train data items and 5,000 validation data items. Post a graph of changes in loss0, loss, WER and BLEU. Loss0 is the loss in the masked target language, and Loss is Loss0 plus the loss in predicting the length of the target sentence.

![fig4](https://github.com/toshiouchi/Machine_Translation/assets/121741811/2c0e8934-366d-4a66-bcc4-ec129e0dcaa7)

![fig5](https://github.com/toshiouchi/Machine_Translation/assets/121741811/6a104ebf-4209-4cd9-9be8-96484e8a1fca)

![fig6](https://github.com/toshiouchi/Machine_Translation/assets/121741811/ca7e1d81-4499-49c2-8f51-ffc071f925bd)

Next, we measured the WER and BLEU values ​​for each epoch and will post them here. In this measurement, the input to the model is only the source language, and information from the training data is not used. For the target input of Transformer Decoder, all sequences except &lt;pad&gt; were iterated 10 times starting from the embedded text of &lt;mask&gt;. The functions used for inference are Fairseq's Mask-Predict.

https://github.com/facebookresearch/Mask-Predict/tree/main

I used it as a reference. length_beam_size was set to 3. This means iterating over three types of length prediction for the target language. Also, batch_size = 1, number_of_test_data = 50.

<table>
<caption>Mask-Preict train data
<th>epoch<th>WER<th>BLEU
<tr><td style="text-align:right;"> 1<td>66.9<td>0.78
<tr><td style="text-align:right;"> 2<td>54.1<td>10.3
<tr><td style="text-align:right;"> 3<td>45.4<td>24.4
<tr><td style="text-align:right;"> 4<td>42.0<td>27.5
<tr><td style="text-align:right;"> 5<td>38.9<td>33.7
<tr><td style="text-align:right;"> 6<td>39.9<td>33.3
<tr><td style="text-align:right;"> 7<td>34.0<td>42.8
<tr><td style="text-align:right;"> 8<td colspan="2">Not measured
<tr><td style="text-align:right;"> 9<td>34.3<td>39.8
<tr><td style="text-align:right;">10<td colspan="2">Not Measured
<tr><td style="text-align:right;">11<td>34.7<td>40.4
<tr><td style="text-align:right;">12<td>32.6<td>44.9
<tr><td style="text-align:right;">13<td>34.8<td>43.0
<tr><td style="text-align:right;">14<td>34.6<td>40.1
<tr><td style="text-align:right;">15<td>35.1<td>37.8
<tr><td style="text-align:right;">16<td>35.8<td>37.4
<tr><td style="text-align:right;">17<td>34.4<td>42.5
<tr><td style="text-align:right;">18<td>33.5<td>39.8
<tr><td style="text-align:right;">19<td>35.1<td>42.1
<tr><td style="text-align:right;">20<td>34.4<td>40.2
</table>

In epoch 12, I got WER 32.6, BLEU 44.9.

## Mask-Predict using CTC loss

I thought that the bottleneck in Mask-Predict's accuracy may be in predicting the sequence length of the target language. I decided to use CTCLoss because I had the impression that it was less dependent on length. In order to use CTCLoss, we must ensure that the sequence length of the target language is longer than the sequence length of the source language, so we upsampled the target language by twice.

We will post graphs of changes in Loss0, Loss, WER, and BLEU for train data and validation data during learning. Loss0 is the CTCLoss of the target sentence calculated without considering the mask. Loss is the sum of this and the loss in predicting the length of the target language.

![fig7](https://github.com/toshiouchi/Machine_Translation/assets/121741811/17496c4e-1f5a-47b6-8083-f1f0e8c605ee)

![fig8](https://github.com/toshiouchi/Machine_Translation/assets/121741811/9dc98ca9-8191-4176-9ffb-682f9e597b08)

![fig9](https://github.com/toshiouchi/Machine_Translation/assets/121741811/9eff8117-829d-44bc-ad27-656514848253)

Similar to Mask-Predict, we evaluated the inference results at each epoch. Mask-Predict inference using CTCLoss was compared using two functions. One is inference, which is a function that decodes the output of the Transformer Decoder in consideration of CTC during the iteration to reduce the number of inference masks, and then reapplies the mask. Another is that in the iteration to reduce the number of inference masks, the position of the mask is adjusted to upasmpling, and the target language reapplies the mask without decoding. The upasmpling of the target language in the Transformer Decoder is not done. Inference1 is a function that does not decode when reapplying the mask.
