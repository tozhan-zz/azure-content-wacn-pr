<properties writer="kathydav" editor="tysonn" manager="jeffreyg" /> 


#如何连接云服务中的虚拟机



当您创建一台虚拟机后，会自动创建一项云服务来包含该虚拟机。您可以在同一云服务下创建多台虚拟机，然后使虚拟机之间能够相互通信，在虚拟机之间进行平衡负载，维护虚拟机的高可用性。

有关对虚拟机进行负载平衡的更多信息，请参见[对虚拟机进行负载平衡][Load balancing virtual machines]。有关管理应用程序可用性的更多信息，请参见[管理虚拟机的可用性][Manage the availability of virtual machines]。


首先，您将需要创建一台虚拟机和新的云服务，然后在该云服务下将其他虚拟机与第一台虚拟机相连接。



1. 使用[如何创建自定义虚拟机] [How to create a custom virtual machine]中的步骤创建虚拟机。


2. 创建第一台自定义虚拟机后，在[管理门户](http://manage.windowsazure.com)命令栏上，单击“新建”。


	![新建虚拟机](./media/howto-connect-vm-cloud-service/Create.png)

3. 单击“虚拟机”，然后单击“从库中”。

	
	![创建自定义虚拟机](./media/howto-connect-vm-cloud-service/CreateNew.png)

	将显示“选择虚拟机操作系统”对话框。


4. 从“选择映像”页中，选择一个映像，然后单击箭头以继续。


	将显示第一个“虚拟机配置”页。


5. 在“虚拟机名称”中，键入您要用于该虚拟机的名称。

6. 在“大小”中，选择要用于该虚拟机的大小。所选大小具体取决于您应用程序所需的内核数。

7. 在“新用户名”中，键入要用于管理服务器的管理帐户的名称。


8. 在“新密码”中，为管理帐户键入一个强密码。在“确认密码”中，重新键入密码。


9. 对于运行 Linux 操作系统的虚拟机，您可以选择通过 SSH 密钥保护虚拟机。


10. 在“云服务”中，选择将包含该新虚拟机的云服务。

11. 在“区域/地缘组/虚拟网络”中，选择要包含该虚拟机的区域。

12. 在“存储帐户”中，选择存储 .vhd 文件的存储帐户，或保留字段的默认设置以自动创建存储帐户。只能自动创建一个存储帐户。使用此设置创建的所有其他虚拟机也位于该存储帐户中。您最多只能创建 20 个存储帐户。


13. 要使用可用性集，选择您在创建第一台虚拟机时创建的可用性集。

14. 查看默认终结点配置，并根据需要修改。

15. 单击复选标记以创建连接的虚拟机。


[How to create a custom virtual machine]: http://windowsazure.com/zh-cn/documentation/articles/virtual-machines-create-custom/
[Load balancing virtual machines]: http://windowsazure.com/zh-cn/documentation/articles/load-balance-virtual-machines/
[Manage the availability of virtual machines]: http://windowsazure.com/zh-cn/documentation/articles/virtual-machines-manage-availability/

