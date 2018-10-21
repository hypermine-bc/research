## Goals

- Is the translation possible? If yes, how?
- Account to UTXO translation
- UTXO to account translation

## Is the translation possible? If yes, how?

Before moving forward, this is the first question that comes into our mind, is it even possible to do this kind of translation between two different models?

Let us start thinking on it and to do it we are going to ask questions, yes a lot of them :P. 

Questions | Source | Target
---| ---| ---|
What is Blockchain? | leadger(λ) | leadger(λ')
What is leadger(λ)? | set of tx | set of tx
What is tx? | [tn, tn-1 .... t1] | [t'n, t'n-1 .... t']
How to form tx? | Account based style | UTXO based style
What is end result? | *effects balance* | *effects balance*

If you see the last questions closely, we can say that, what ever be happening in these two different kind of blockchains, the *end result* is going to be the same for sure, i.e *effects balance*.

Hence,

> I can say that it is possible to translate between these two model since end of the day the result is same

How it is possible? Let us look into that in the next sections.

## Account to UTXO translation

Before seeing this translation, let us recall how Bitcoin transaction works?

A transaction in Bitcoin, which is a UTXO based styled blockchain, is nothing but combination of **Inputs(I)** and **Outputs(O)** (ofcourse there are other parameters, but let us just ignore them for simplicity for now)
- **Inputs(I)** :  nothing but *unspentOutputs(O)* for that sender, which leads to in the calculation of **balance(B)** ![balance of ](https://latex.codecogs.com/gif.latex?%5Csum_%7Bo%20%5Cin%20O%7Do.value) where, `o.address = sender`

- **Output(O)** : is list of all receivers with theirs respective amounts along with the change for sender himself.
    
    ```
        spents + change = List [(receiver, value)] + (sender, value)
    ```
Keeping the above in mind:

**Problem**

To convert account based transaction (t) in source ledger (λ) to utxo based transaction t' in target ledger (λ')

**Input**
```
t = (sender, receiver, value, fee, nounce) 
```

**Output**

```
t' = (Inputs, Outputs, fee)
```

---

If we have to convert account based transaction (t) in source ledger (λ) to utxo based transaction t' in target ledger (λ') :

![translation](https://latex.codecogs.com/gif.latex?%5Ctau%20%5E%7Bacc%7D_%7Butxo%7D%20%3D%20UtxoTx%28I%2CO%2Cf%29)

We have to find, **I**, **O** and **f**, where, I is inputs, O is outputs and f is fee for the transaction.

### Inputs 

Get all outputs from  *unspentOutputs(O)* in target ledger (λ') with respect to sender, s, such that;

![inputs](https://latex.codecogs.com/gif.latex?%5B%7B%5Csum_%7Bi%20%5Cin%20O%7D%20%7D_%7Bi.addr%20%3D%20s%7D%20i.value%20%3E%3D%20t.valule%20&plus;t.fee%5D%20---%281%29)

### Outputs

Outputs are basically list of outputs, but here we need only 2 outputs,

- one *Output(r,v)* - for receiver
- the other *Output(s, (B - t.value))* - for sender himself, as change, where ![](https://latex.codecogs.com/gif.latex?B%20%3D%20%7B%5Csum_%7Bi%20%5Cin%20O%7D%20%7D_%7Bi.addr%20%3D%20s%7D%20i.value)

```
O = Output(r,v) + Output(s,v) ------- (2)
```

### Fee
```
Fee  = t.fee ----------- (3)
```


## UTXO to Account 

Now to do this, let us recall how does datastructure of account based and utxo based model looks like from this table:

Account based tx | Utxo based tx
---| ---|
sender(s)| List[input] = I 
receiver(r)| List[output] = O
value(v)|
fee(f) | fee
nonce(n) |

from the above table we can write this :
```
UTXO = I, O, f
     ~= List(input , output, f)
     ~= List((s) , (r,v), f)
     ~= List(account)
```

> which means a UTXO can be thought of list of accounts because, any account based transaction can have only one receiver, whereas a UTXO based transaction has more than one sender or more than one receiver. 


## Reference
- https://eprint.iacr.org/2018/262.pdf 
- https://qtum.org/user/pages/03.tech/01.white-papers/Qtum%20Whitepaper.pdf
