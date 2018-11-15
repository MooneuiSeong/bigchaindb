<!---
Copyright BigchainDB GmbH and BigchainDB contributors
SPDX-License-Identifier: (Apache-2.0 AND CC-BY-4.0)
Code is Apache-2.0 and docs are CC-BY-4.0
--->

# Transaction Concepts

트랜잭션은 물건 (예 : 자산)을 등록, 발행, 생성 또는 전송하는 데 사용됩니다.

트랜잭션은 BigchainDB가 저장하는 가장 기본적인 종류의 레코드입니다. CREATE 트랜잭션과 TRANSFER 트랜잭션의 두 종류가 있습니다.


## Transactions 생성

CREATE 트랜잭션은 BigchainDB에서 한 가지 (또는 자산)의 이력을 등록, 발행, 생성 또는 다른 방법으로 시작하는 데 사용될 수 있습니다. 예를 들어, 신원이나 창작물을 등록 할 수 있습니다. 이러한 것들을 종종 "자산"이라고 부르지 만 문자 적 ??자산이 아닐 수도 있습니다.

BigchainDB는 BigchainDB Server v0.8.0부터 나눌 수있는 자산을 지원합니다. 이는 "공유"의 초기 숫자로 자산을 생성 / 등록 할 수 있음을 의미합니다. 예를 들어, CREATE 트랜잭션은 50 개의 오크 나무로 된 트럭로드를 등록 할 수 있습니다. 분할 가능한 자산의 각 주식은 서로 공유 할 수 있어야합니다. 주식은 대체 가능해야합니다.

A CREATE transaction can have one or more outputs.
Each output has an associated amount: the number of shares tied to that output.
For example, if the asset consists of 50 oak trees,
one output might have 35 oak trees for one set of owners,
and the other output might have 15 oak trees for another set of owners.

Each output also has an associated condition: the condition that must be met
(by a TRANSFER transaction) to transfer/spend the output.
BigchainDB supports a variety of conditions.
For details, see
the section titled **Transaction Components: Conditions**
in the relevant
[BigchainDB Transactions Spec](https://github.com/bigchaindb/BEPs/tree/master/tx-specs/).

![Example BigchainDB CREATE transaction](./_static/CREATE_example.png)

Above we see a diagram of an example BigchainDB CREATE transaction.
It has one output: Pam owns/controls three shares of the asset
and there are no other shares (because there are no other outputs).

Each output also has a list of all the public keys associated
with the conditions on that output.
Loosely speaking, that list might be interpreted as the list of "owners."
A more accurate word might be fulfillers, signers, controllers,
or transfer-enablers.
See the section titled **A Note about Owners**
in the relevant [BigchainDB Transactions Spec](https://github.com/bigchaindb/BEPs/tree/master/tx-specs/).

A CREATE transaction must be signed by all the owners.
(If you're looking for that signature,
it's in the one "fulfillment" of the one input, albeit encoded.)

## TRANSFER Transactions

A TRANSFER transaction can transfer/spend one or more outputs
on other transactions (CREATE transactions or other TRANSFER transactions).
Those outputs must all be associated with the same asset;
a TRANSFER transaction can only transfer shares of one asset at a time.

Each input on a TRANSFER transaction connects to one output
on another transaction.
Each input must satisfy the condition on the output it's trying
to transfer/spend.

A TRANSFER transaction can have one or more outputs,
just like a CREATE transaction (described above).
The total number of shares coming in on the inputs must equal
the total number of shares going out on the outputs.

![Example BigchainDB transactions](./_static/CREATE_and_TRANSFER_example.png)

Above we see a diagram of two example BigchainDB transactions,
a CREATE transaction and a TRANSFER transaction.
The CREATE transaction is the same as in the earlier diagram.
The TRANSFER transaction spends Pam's output,
so the input on that TRANSFER transaction must contain a valid signature
from Pam (i.e. a valid fulfillment).
The TRANSFER transaction has two outputs:
Jim gets one share, and Pam gets the remaining two shares.

Terminology: The "Pam, 3" output is called a "spent transaction output"
and the "Jim, 1" and "Pam, 2" outputs are called "unspent transaction outputs"
(UTXOs).

**Example 1:** Suppose a red car is owned and controlled by Joe.
Suppose the current transfer condition on the car says
that any valid transfer must be signed by Joe.
Joe could build a TRANSFER transaction containing
an input with Joe's signature (to fulfill the current output condition)
plus a new output condition saying that any valid transfer
must be signed by Rae.

**Example 2:** Someone might construct a TRANSFER transaction
that fulfills the output conditions on four
previously-untransferred assets of the same asset type
e.g. paperclips. The amounts might be 20, 10, 45 and 25, say,
for a total of 100 paperclips.
The TRANSFER transaction would also set up new transfer conditions.
For example, maybe a set of 60 paperclips can only be transferred
if Gertrude signs, and a separate set of 40 paperclips can only be
transferred if both Jack and Kelly sign.
Note how the sum of the incoming paperclips must equal the sum
of the outgoing paperclips (100).

## Transaction Validity

When a node is asked to check if a transaction is valid, it checks several
things. We documented those things in a post on *The BigchainDB Blog*:
["What is a Valid Transaction in BigchainDB?"](https://blog.bigchaindb.com/what-is-a-valid-transaction-in-bigchaindb-9a1a075a9598)
(Note: That post was about BigchainDB Server v1.0.0.)

Each [BigchainDB Transactions Spec](https://github.com/bigchaindb/BEPs/tree/master/tx-specs/) documents the conditions for a transaction (of that version) to be valid.

## Example Transactions

There are example BigchainDB transactions in
[the HTTP API documentation](https://docs.bigchaindb.com/projects/server/en/latest/http-client-server-api.html)
and
[the Python Driver documentation](https://docs.bigchaindb.com/projects/py-driver/en/latest/usage.html).
