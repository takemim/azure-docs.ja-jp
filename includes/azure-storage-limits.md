| リソース | 既定の制限 |
| --- | --- |
| サブスクリプションあたりのストレージ アカウント数 | 200<sup>1</sup> |
| ストレージ アカウントの最大容量 | 500 TiB<sup>2</sup> |
| ストレージ アカウントあたりの BLOB コンテナー、BLOB、ファイル共有、テーブル、キュー、エンティティ、メッセージの最大数 | 制限なし |
| ストレージ アカウントあたりの最大要求レート | 20,000 RPS (1 秒あたりの要求数)<sup>2</sup> |
| ストレージ アカウントあたりの最大受信速度<sup>3</sup> (米国リージョン) | GRS/ZRS<sup>4</sup> が有効な場合は 10 Gbps、LRS<sup>2</sup> の場合は 20 Gbps |
| ストレージ アカウントあたりの最大送信速度<sup>3</sup> (米国リージョン) | RA-GRS/GRS/ZRS<sup>4</sup> が有効な場合は 20 Gbps、LRS<sup>2</sup> の場合は 30 Gbps |
| ストレージ アカウントあたりの最大受信速度<sup>3</sup> (米国以外のリージョン) | GRS/ZRS<sup>4</sup> が有効な場合は 5 Gbps、LRS<sup>2</sup> の場合は 10 Gbps |
| ストレージ アカウントあたりの最大送信速度<sup>3</sup> (米国以外のリージョン) | RA-GRS/GRS/ZRS<sup>4</sup> が有効な場合は 10 Gbps、LRS<sup>2</sup> の場合は 15 Gbps |

<sup>1</sup> Standard および Premium ストレージ アカウントの両方が含まれます。 必要なストレージ アカウントが 200 個を超える場合は、[Azure サポート](https://azure.microsoft.com/support/faq/)からリクエストを送信してください。 Azure Storage チームがビジネス ケースを確認します。承認された場合、最大 250 個のストレージ アカウントが与えられます。 

<sup>2</sup> ストレージ アカウントの制限を拡張する必要がある場合は、[Azure サポート](https://azure.microsoft.com/support/faq/)にお問い合わせください。 Azure Storage チームがリクエストを審査し、ケース バイ ケースで上限の引き上げを承認します。 汎用のストレージ アカウントと BLOB ストレージ アカウントの両方で、容量の増加、イングレス/エグレス、および要求別の要求レートをサポートします。 BLOB ストレージ アカウントの新しい最大値については、「[Announcing larger, higher scale storage accounts](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)」(大規模な高スケールのストレージ アカウントの発表) を参照してください。

<sup>3</sup> アカウントの送受信制限のみが適用されます。 "*受信*" とは、ストレージ アカウントに送信されるすべてのデータ (要求) のことです。 *送信* とはストレージ アカウントから送信されるすべてのデータ (応答) のことです。  

<sup>4</sup> Azure Storage 冗長オプションには次が含まれます。
* **RA-GRS**: 読み取りアクセス地理冗長ストレージ。 RA-GRS が有効な場合、2 次拠点への送信ターゲットは、1 次拠点と同じになります。
* **GRS**: geo 冗長ストレージ。 
* **ZRS**: ゾーン冗長ストレージ。 ブロック BLOB でのみ使用できます。 
* **LRS**: ローカル冗長ストレージ。 
