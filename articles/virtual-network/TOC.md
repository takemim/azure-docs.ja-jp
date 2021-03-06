# [Virtual Network のドキュメント](index.md)

# 概要
## [仮想ネットワーク](virtual-networks-overview.md)
## [ルーティング](virtual-networks-udr-overview.md)
## [仮想ネットワーク ピアリング](virtual-network-peering-overview.md)
## [仮想ネットワーク サービスのエンドポイント](virtual-network-service-endpoints-overview.md)
## [Azure サービス用の仮想ネットワーク](virtual-network-for-azure-services.md)
## [セキュリティ](security-overview.md)
## [コンテナー ネットワーク](container-networking.md)
## [ビジネス継続性](virtual-network-disaster-recovery-guidance.md)
## [IP アドレス指定](virtual-network-ip-addresses-overview-arm.md)
## [DDoS Protection](ddos-protection-overview.md)
## [FAQ](virtual-networks-faq.md)
## クラシック
### [IP アドレス指定](virtual-network-ip-addresses-overview-classic.md)
### [アクセス制御リスト](virtual-networks-acl.md)

# 作業の開始
## [仮想ネットワークの作成 - ポータル](quick-create-portal.md)
## [仮想ネットワークの作成 - PowerShell](quick-create-powershell.md)
## [仮想ネットワークの作成 - Azure CLI](quick-create-cli.md)

# 方法
## 計画と設計
### [仮想ネットワーク](virtual-network-vnet-plan-design-arm.md)
### [ネットワーク セキュリティ グループ](virtual-networks-nsg.md)

## デプロイ

### ネットワーク セキュリティ グループ
#### [Azure Portal](virtual-networks-create-nsg-arm-pportal.md)
#### [Azure PowerShell](virtual-networks-create-nsg-arm-ps.md)
#### [Azure CLI](virtual-networks-create-nsg-arm-cli.md)
#### [テンプレート](virtual-networks-create-nsg-arm-template.md)
#### [アプリケーション セキュリティ グループ](create-network-security-group-preview.md)
#### クラシック
##### [Azure PowerShell](virtual-networks-create-nsg-classic-ps.md)
##### [Azure CLI 1.0](virtual-networks-create-nsg-classic-cli.md)

### ルート テーブル
#### [Azure Portal](tutorial-create-route-table-portal.md)
#### [Azure PowerShell](tutorial-create-route-table-powershell.md)
#### [Azure CLI](tutorial-create-route-table-cli.md)
#### [テンプレート](virtual-network-create-udr-arm-template.md)
#### クラシック
##### [Azure PowerShell](virtual-network-create-udr-classic-ps.md)
##### [Azure CLI](virtual-network-create-udr-classic-cli.md)

### 仮想ネットワーク ピアリング
#### 同じデプロイメント モデル - 同じサブスクリプション
##### [Azure Portal](tutorial-connect-virtual-networks-portal.md)
##### [Azure PowerShell](tutorial-connect-virtual-networks-powershell.md)
##### [Azure CLI](tutorial-connect-virtual-networks-cli.md)
#### [同じデプロイメント モデル - 異なるサブスクリプション](create-peering-different-subscriptions.md)
#### [異なるデプロイメント モデル - 同じサブスクリプション](create-peering-different-deployment-models.md)
#### [異なるデプロイメント モデル - 異なるサブスクリプション](create-peering-different-deployment-models-subscriptions.md)

### Virtual Network サービスのエンドポイント
#### [Azure Portal](tutorial-restrict-network-access-to-resources.md)
#### [Azure PowerShell](tutorial-restrict-network-access-to-resources-powershell.md)
#### [Azure CLI](tutorial-restrict-network-access-to-resources-cli.md)

### 仮想マシン
#### [仮想マシン ネットワーク スループット](virtual-machine-network-throughput.md)
#### 静的パブリック IP アドレスを持つ VM を作成する
##### [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)
##### [Azure PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
##### [Azure CLI](virtual-network-deploy-static-pip-arm-cli.md)
##### [テンプレート](virtual-network-deploy-static-pip-arm-template.md)
##### クラシック
###### [Azure PowerShell](virtual-networks-reserved-public-ip.md)

#### VM の作成 - 静的プライベート IP アドレス
##### [Azure Portal](virtual-networks-static-private-ip-arm-pportal.md)
##### [Azure PowerShell](virtual-networks-static-private-ip-arm-ps.md)
##### [Azure CLI](virtual-networks-static-private-ip-arm-cli.md)
##### クラシック
###### [Azure Portal](virtual-networks-static-private-ip-classic-pportal.md)
###### [Azure PowerShell](virtual-networks-static-private-ip-classic-ps.md)
###### [Azure CLI](virtual-networks-static-private-ip-classic-cli.md)

#### VM の作成 - 複数のネットワーク インターフェイス
##### [Azure PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [Azure CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [テンプレート](virtual-network-deploy-multinic-arm-template.md)

##### クラシック
###### [Azure PowerShell](virtual-network-deploy-multinic-classic-ps.md)
###### [Azure CLI](virtual-network-deploy-multinic-classic-cli.md)

#### VM の作成 - 複数の IP アドレス
##### [Azure Portal](virtual-network-multiple-ip-addresses-portal.md)
##### [Azure PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
##### [Azure CLI](virtual-network-multiple-ip-addresses-cli.md)
##### [テンプレート](virtual-network-multiple-ip-addresses-template.md)

#### VM の作成 - 高速ネットワーク
##### [Azure PowerShell](create-vm-accelerated-networking-powershell.md)
##### [Azure CLI](create-vm-accelerated-networking-cli.md)

### 接続のシナリオ
#### [仮想ネットワーク (VNet) から VNet に](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [VNet (Resource Manager) から VNet に (クラシック)](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [VNet からオンプレミスのネットワークに (VPN)](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [VNet からオンプレミスのネットワークに (ExpressRoute)](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [高可用性ハイブリッド ネットワーク アーキテクチャ](../guidance/guidance-hybrid-network-expressroute-vpn-failover.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

### セキュリティのシナリオ
#### [仮想アプライアンスでネットワークをセキュリティで保護する](virtual-network-scenario-udr-gw-nva.md)
#### [Azure とインターネットの間の DMZ](../guidance/guidance-iaas-ra-secure-vnet-dmz.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [クラウド サービスとネットワーク セキュリティ](../best-practices-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [NSG を使用した DMZ の作成](virtual-networks-dmz-nsg.md)
##### [NSG を使用した DMZ の作成 (クラシック)](virtual-networks-dmz-nsg-asm.md)
##### [ファイアウォールと NSG を使用した DMZ の作成 (クラシック)](virtual-networks-dmz-nsg-fw-asm.md)
##### [ファイアウォール、UDR、NSG を使用した DMZ (クラシック)](virtual-networks-dmz-nsg-fw-udr-asm.md)

##### [サンプル アプリケーション](virtual-networks-sample-app.md)

### クラシック
#### [Virtual Network](create-virtual-network-classic.md)
##### [Azure Portal](virtual-networks-create-vnet-classic-pportal.md)
##### [Azure PowerShell](virtual-networks-create-vnet-classic-netcfg-ps.md)
##### [Azure CLI](virtual-networks-create-vnet-classic-cli.md)
#### [仮想ネットワーク構成ファイルでの DNS 設定の指定](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)
#### [サービス構成ファイルでの DNS 設定の指定](virtual-networks-specifying-dns-settings-in-a-service-configuration-file.md)

## 構成
### 仮想マシン
#### [ネットワーク インターフェイスの追加または削除](virtual-network-network-interface-vm.md)
#### [VM とクラウド サービスの名前解決](virtual-networks-name-resolution-for-vms-and-role-instances.md)
#### [動的 DNS を使用して独自の DNS サーバーでホスト名を登録する](virtual-networks-name-resolution-ddns.md)
#### [ネットワーク スループットの最適化](virtual-network-optimize-network-bandwidth.md)
#### [ホスト名の表示および変更](virtual-networks-viewing-and-modifying-hostnames.md)
#### クラシック
##### 静的 IP アドレス
###### [PowerShell](virtual-networks-reserved-private-ip.md)
###### [CLI](virtual-networks-static-private-ip-classic-cli.md)
##### [インスタンス レベル パブリック IP アドレス](virtual-networks-instance-level-public-ip.md)

### クラシック
#### アクセス制御リスト
##### [Azure Portal](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [Azure PowerShell](virtual-networks-acl-powershell.md)

## [管理]
### [仮想ネットワーク](manage-virtual-network.md)
#### [サブネット](virtual-network-manage-subnet.md)
#### [ピアリング](virtual-network-manage-peering.md)
#### クラシック
##### [ネットワーク構成ファイル](virtual-networks-using-network-configuration-file.md)
##### [アフィニティ グループからリージョンへの移行](virtual-networks-migrate-to-regional-vnet.md)
### ネットワーク セキュリティ グループ
#### [Azure Portal](virtual-network-manage-nsg-arm-portal.md)
#### [Azure PowerShell](virtual-network-manage-nsg-arm-ps.md)
#### [Azure CLI](virtual-network-manage-nsg-arm-cli.md)

#### [ログ](virtual-network-nsg-manage-log.md)
### [ルート テーブル](manage-route-table.md)
### ネットワーク インターフェイス (NIC)
#### [NIC の作成、変更、削除](virtual-network-network-interface.md)
#### [IP アドレスの追加、変更、削除](virtual-network-network-interface-addresses.md)
### 仮想マシン
#### [VM を別のサブネットに移動する](virtual-networks-move-vm-role-to-subnet.md)
### [パブリック IP アドレス](virtual-network-public-ip-address.md)
### DDoS Protection
#### [Azure Portal](ddos-protection-manage-portal.md)
#### [Azure PowerShell](ddos-protection-manage-ps.md)

## トラブルシューティング
### ネットワーク セキュリティ グループ
#### [Azure Portal](virtual-network-nsg-troubleshoot-portal.md)
#### [Azure PowerShell](virtual-network-nsg-troubleshoot-powershell.md)
### ルート
#### [Azure Portal](virtual-network-routes-troubleshoot-portal.md)
#### [Azure PowerShell](virtual-network-routes-troubleshoot-powershell.md)
### [スループットのテスト](virtual-network-bandwidth-testing.md)
### [仮想ネットワークを削除できない](virtual-network-troubleshoot-cannot-delete-vnet.md)
### [VM 間の接続に関する問題](virtual-network-troubleshoot-connectivity-problem-between-vms.md)
### [SMTP バナー チェック用の PTR の構成](create-ptr-for-smtp-service.md)

## サンプルのスクリプト
### [Azure CLI](cli-samples.md)
### [Azure PowerShell](powershell-samples.md)

# リファレンス
## [コード サンプル](https://azure.microsoft.com/resources/samples/?service=virtual-network)
## [Azure PowerShell (Resource Manager)](/powershell/module/azurerm.network)
## [Azure PowerShell (クラシック)](/powershell/module/azure/)
## [Azure CLI](/cli/azure/network)
## [Java](/java/api/)
## [REST (Resource Manager)](https://msdn.microsoft.com/library/mt163658.aspx)
## [REST (クラシック)](https://msdn.microsoft.com/library/jj157182.aspx)


# 関連項目
## [Virtual Machines](/azure/virtual-machines/)
## [Application Gateway](/azure/application-gateway/)
## [Azure DNS](/azure/dns/)
## [Traffic Manager](/azure/traffic-manager/)
## [Load Balancer](/azure/load-balancer/)
## [VPN Gateway](/azure/vpn-gateway/)
## [ExpressRoute](/azure/expressroute/)

# リソース
## [Azure のロードマップ](https://azure.microsoft.com/roadmap/?category=networking)
## [ネットワークのブログ](http://azure.microsoft.com/blog/topics/networking)
## [ネットワークのフォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesVirtualNetwork)
## [料金](https://azure.microsoft.com/pricing/details/virtual-network)
## [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-virtual-network)
## [ネットワーク リソース プロバイダー](resource-groups-networking.md)
