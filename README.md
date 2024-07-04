Machine training data for Mask-Predict machine translation.

Machine training data for a model that upsamples EncoderOuts and uses it as target input of Decoder to calculate CTCLoss.

## Introduction

This report reports on training data for machine translation that translates Japanese to English.

What is noteworthy is that the non-autoregressive program had a smaller WER and a larger BLEU than the autoregressive program.

The report covers the following four areas.

1.Autoregressive learning using Transformer.  
  WER 29.1 BLEU 47.9
  Program file name Autoregressive.ipynb.

2.Mask-Predict non-autoregressive learning. Program file name Mask_Predict.ipynb.

3.Non-autoregressive learning with CTCLoss and Mask-Predict. Program file name CTC_Mask_Predict.ipynb.

4.Upsampling the Encoder output to the Decoder target input. loss is CTC. Program file name EncoderOuts_Upsampling.ipynb. WER 24.1、BLEU 55.1

## Learning Data

The data is from the Japanese-English corpus.

https://www.kecl.ntt.co.jp/icl/lirg/jparacrawl/

The train is 1 million, the val is 5000, and the test is 50. In my environment, if an English sentence contains multi-byte characters, the separated English characters in the idx_to_word.json file become garbled, so I did not use English sentences that contained multi-byte characters for learning.

## Autoregressive learning

Learning was performed for 20 epochs with 1 million train data items and 5,000 validation data items. Post a graph of loss and WER changes.

<div align="center">
  
![fig1](https://github.com/toshiouchi/Machine_Translation/assets/121741811/dc68910a-2759-4607-90c1-6847d18d1bfa)

![fig2](https://github.com/toshiouchi/Machine_Translation/assets/121741811/e113eeed-a83b-4bff-a782-3c2ed87a0063)

</div>

During this training, we measured WER and some BLEU values. The values ​​in the graph above are obtained by inputting target[:,1:] to the target input of the Transformer Decoder, but for WER and BLEU in the table below, only the source language is input to the model. No training data is used for the target input of the Transformer Decoder. These are numbers obtained by using the model in an autoregressive manner.

<div align="center">
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
</div>
WER 29.1 and BLEU 47.9 are obtained at 20 epochs.

## Learning with Mask-Predict.

Learning was performed for 20 epochs with 1 million train data items and 5,000 validation data items. Post a graph of changes in loss0, loss, WER and BLEU. Loss0 is the loss in the masked target language, and Loss is Loss0 plus the loss in predicting the length of the target sentence.

<div align="center">

![fig3](https://github.com/toshiouchi/Machine_Translation/assets/121741811/6ba38474-d5fe-4a68-a927-02889a7bb469)

![fig4](https://github.com/toshiouchi/Machine_Translation/assets/121741811/2c0e8934-366d-4a66-bcc4-ec129e0dcaa7)

![fig5](https://github.com/toshiouchi/Machine_Translation/assets/121741811/6a104ebf-4209-4cd9-9be8-96484e8a1fca)

![fig6](https://github.com/toshiouchi/Machine_Translation/assets/121741811/ca7e1d81-4499-49c2-8f51-ffc071f925bd)

</div>

Next, we measured the WER and BLEU values ​​for each epoch and will post them here. In this measurement, the input to the model is only the source language, and information from the training data is not used. For the target input of Transformer Decoder, all sequences except &lt;pad&gt; were iterated 10 times starting from the embedded text of &lt;mask&gt;. The functions used for inference are Fairseq's Mask-Predict.

https://github.com/facebookresearch/Mask-Predict/tree/main

I used it as a reference. length_beam_size was set to 3. This means iterating over three types of length prediction for the target language. Also, batch_size = 1, number_of_test_data = 50.

<div align="center">
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
</div>
In epoch 12, I got WER 32.6, BLEU 44.9.

## Mask-Predict using CTC loss

I thought that the bottleneck in Mask-Predict's accuracy may be in predicting the sequence length of the target language. I decided to use CTCLoss because I had the impression that it was less dependent on length. In order to use CTCLoss, we must ensure that the sequence length of the target language is longer than the sequence length of the source language, so we upsampled the target language by twice.

We will post graphs of changes in Loss0, Loss, WER, and BLEU for train data and validation data during learning. Loss0 is the CTCLoss of the target sentence calculated without considering the mask. Loss is the sum of this and the loss in predicting the length of the target language.

<div align="center">

![fig7](https://github.com/toshiouchi/Machine_Translation/assets/121741811/17496c4e-1f5a-47b6-8083-f1f0e8c605ee)

![fig8](https://github.com/toshiouchi/Machine_Translation/assets/121741811/9dc98ca9-8191-4176-9ffb-682f9e597b08)

![fig9](https://github.com/toshiouchi/Machine_Translation/assets/121741811/9eff8117-829d-44bc-ad27-656514848253)

![fig10](https://github.com/toshiouchi/Machine_Translation/assets/121741811/c0fd471c-09eb-4c3b-b2da-bdb38539d3b8)

</div>

Similar to Mask-Predict, we evaluated the inference results at each epoch. Mask-Predict inference using CTCLoss was compared using two functions. One is inference, which is a function that decodes the output of the Transformer Decoder in consideration of CTC during the iteration to reduce the number of inference masks, and then reapplies the mask. Another is that in the iteration to reduce the number of inference masks, the position of the mask is adjusted to upasmpling, and the target language reapplies the mask without decoding. The upasmpling of the target language in the Transformer Decoder is not done. Inference1 is a function that does not decode when reapplying the mask.

Length_beam_size was set to 3. This means iterating on three types of target language predictions. Also, batch_size = 1, number_of_test_data = 50.

Initially, we introduced CTCLoss to weaken the length dependence of the predicted target language, but the value of 3 was better than length_beam_size = 2,4,5.

<div align="center">
<table>
<caption>CTC Mask-Preict train inference func. data
<th>　　epoch<th>　　　　WER<th>　　　BLEU
<tr><td style="text-align:right;"> 1<td>76.0<td>0.00
<tr><td style="text-align:right;"> 2<td>59.8<td>16.5
<tr><td style="text-align:right;"> 3<td>52.8<td>24.3
<tr><td style="text-align:right;"> 4<td>57.3<td>22.2
<tr><td style="text-align:right;"> 5<td>48.2<td>25.2
<tr><td style="text-align:right;"> 6<td>51.0<td>25.0
<tr><td style="text-align:right;"> 7<td>45.2<td>29.4
<tr><td style="text-align:right;"> 8<td>44.2<td>28.4
<tr><td style="text-align:right;"> 9<td>46.1<td>28.3
<tr><td style="text-align:right;">10<td>43.8<td>30.7
<tr><td style="text-align:right;">11<td>42.0<td>37.5
<tr><td style="text-align:right;">12<td>41.8<td>35.3
<tr><td style="text-align:right;">13<td>40.9<td>37.4
<tr><td style="text-align:right;">14<td>41.8<td>36.1
<tr><td style="text-align:right;">15<td>39.7<td>36.5
<tr><td style="text-align:right;">16<td>40.1<td>36.8
<tr><td style="text-align:right;">17<td>40.5<td>34.1
<tr><td style="text-align:right;">18<td>38.0<td>38.4
<tr><td style="text-align:right;">19<td>39.8<td>35.5
<tr><td style="text-align:right;">20<td>34.1<td>42.6
</table>
</div>

<div align="center">
<table>
<caption>CTC Mask-Preict train inference1 func. data
<th>　　epoch<th>　　　　WER<th>　　　BLEU
<tr><td style="text-align:right;"> 1<td>73.2<td>0.00
<tr><td style="text-align:right;"> 2<td>56.0<td>13.3
<tr><td style="text-align:right;"> 3<td>49.4<td>20.8
<tr><td style="text-align:right;"> 4<td>41.5<td>27.1
<tr><td style="text-align:right;"> 5<td>45.1<td>24.7
<tr><td style="text-align:right;"> 6<td>38.4<td>33.3
<tr><td style="text-align:right;"> 7<td>34.0<td>34.9
<tr><td style="text-align:right;"> 8<td>38.8<td>35.2
<tr><td style="text-align:right;"> 9<td>39.4<td>30.9
<tr><td style="text-align:right;">10<td>39.4<td>34.9
<tr><td style="text-align:right;">11<td>37.0<td>37.5
<tr><td style="text-align:right;">12<td>38.0<td>37.0
<tr><td style="text-align:right;">13<td>40.2<td>29.2
<tr><td style="text-align:right;">14<td>37.9<td>34.9
<tr><td style="text-align:right;">15<td>38.9<td>36.6
<tr><td style="text-align:right;">16<td>38.9<td>34.4
<tr><td style="text-align:right;">17<td>40.5<td>28.5
<tr><td style="text-align:right;">18<td>37.9<td>33.1
<tr><td style="text-align:right;">19<td>33.2<td>45.3
<tr><td style="text-align:right;">20<td>37.2<td>34.7
</table>
</div>

WER 33.2 BLEU 45.3 in epoch 19 of inferece1 were got.

## A non-autoregressive program that upsamples the output of TransformerEncoder and makes it the target input of TransformerDecoder. Loss is CTC.

In Mask-Predict using CTC loss, the masked target sentence (teacher data) is upsampled twice in the sequence direction and becomes the target input of the TransformerDecoder. On the other hand, in the method of posting the evaluation values ​​here, the output of TransformerEncoder is upsampled twice in the sequence direction and used as the target input of TransformerDecoder. The loss is CTC Loss.

Training continued for up to 20 epochs.

I will post graphs of loss, WER, and BLEU for train data and validation data. In this program, the neural network for learning and inference can be predictions = model( source_embed_sentences ) and does not depend on target_embed_sentences. In other words, learning and inference can use the same algorithm's model function. Inference is non-autoregressive.

<div align="center">

![fig11](https://github.com/toshiouchi/Machine_Translation/assets/121741811/113047e4-4f72-464e-a43f-8976a2e1f8d3)

![fig12](https://github.com/toshiouchi/Machine_Translation/assets/121741811/b4309c4e-af71-4e96-89a8-4eabcd3b733f)

</div>

Next, we will post a table of inference evaluation for test data. The number of test data is 50 and batch_size = 1.

<div align="center">
<table>
<caption>EncoderOuts Upsampling inference evaluation values
<th>　　　　　epoch<th>　　　　　　　WER<th>　　　　　　BLEU
<tr><td style="text-align:right;"> 1<td>41.0<td>25.2
<tr><td style="text-align:right;"> 2<td>34.4<td>33.3
<tr><td style="text-align:right;"> 3<td>32.7<td>34.6
<tr><td style="text-align:right;"> 4<td>34.0<td>37.2
<tr><td style="text-align:right;"> 5<td>34.0<td>34.1
<tr><td style="text-align:right;"> 6<td>31.4<td>40.1
<tr><td style="text-align:right;"> 7<td>32.5<td>37.5
<tr><td style="text-align:right;"> 8<td>29.8<td>42.4
<tr><td style="text-align:right;"> 9<td>29.6<td>44.8
<tr><td style="text-align:right;">10<td>26.9<td>47.8
<tr><td style="text-align:right;">11<td>30.0<td>45.6
<tr><td style="text-align:right;">12<td>26.7<td>50.3
<tr><td style="text-align:right;">13<td colspan="2">Not measured
<tr><td style="text-align:right;">14<td>25.6<td>50.8
<tr><td style="text-align:right;">15<td>25.3<td>50.8
<tr><td style="text-align:right;">16<td>25.4<td>51.5
<tr><td style="text-align:right;">17<td>24.1<td>55.1
<tr><td style="text-align:right;">18<td>23.2<td>54.1
<tr><td style="text-align:right;">19<td>26.0<td>50.2
<tr><td style="text-align:right;">20<td>24.0<td>52.5
</table>
</div>

In epoch 17, I got WER 24.1 and BLEU 55.1. In general, the accuracy of the non-autoregressive type is lower than that of the autoregressive type, but in this experiment, it is worth noting that the accuracy of the autoregressive type was WER 29.1 and BLEU 47.9.
