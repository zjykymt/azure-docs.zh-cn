---
title: 在 Azure Stack VM 中备份文件
description: 使用 Azure 备份将 Azure Stack 文件与应用程序备份和恢复到 Azure Stack 环境。
ms.topic: conceptual
ms.date: 06/05/2018
ms.openlocfilehash: cae95c10c510969cc0553a54a506789d6be427d7
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2020
ms.locfileid: "89180977"
---
# <a name="back-up-files-and-applications-on-azure-stack"></a>在 Azure Stack 上备份文件和应用程序

可以使用 Azure 备份保护（或备份）Azure Stack 上的文件和应用程序。 若要备份文件和应用程序，请将 Microsoft Azure 备份服务器安装为 Azure Stack 上运行的虚拟机。 可以保护相同虚拟网络中任何 Azure Stack 服务器上的文件。 安装 Azure 备份服务器后，请添加 Azure 磁盘以增加用于短期备份数据的本地存储。 Azure 备份服务器将 Azure 存储用于长期保留。

> [!NOTE]
> 尽管 Azure 备份服务器和 System Center Data Protection Manager (DPM) 类似，但不支持将 DPM 与 Azure Stack 一起使用。
>

本文不涉及在 Azure Stack 环境中安装 Azure 备份服务器。 若要在 Azure Stack 中安装 Azure 备份服务器，请参阅[安装 Azure 备份服务器](backup-mabs-install-azure-stack.md)一文。

## <a name="back-up-files-and-folders-in-azure-stack-vms-to-azure"></a>将 Azure Stack VM 中的文件和文件夹备份到 Azure

若要将 Azure 备份服务器配置为保护 Azure Stack 虚拟机中的文件，请打开 Azure 备份服务器控制台。 将使用控制台配置保护组，以及保护虚拟机上的数据。

1. 在 Azure 备份服务器控制台中，选择 " **保护** "，并在工具栏中选择 " **新建** " 以打开 " **创建新保护组** 向导"。

   ![在 Azure 备份服务器控制台中配置保护](./media/backup-mabs-files-applications-azure-stack/1-mabs-menu-create-protection-group.png)

    打开向导可能需要花费几秒钟。 向导打开后，选择 " **下一步** " 以转到 " **选择保护组类型** " 屏幕。

   ![“新建保护组”向导打开](./media/backup-mabs-files-applications-azure-stack/2-create-new-protection-group-wiz.png)

2. 在 " **选择保护组类型** " 屏幕上选择 " **服务器** "，然后选择 " **下一步**"。

    ![选择保护组类型](./media/backup-mabs-files-applications-azure-stack/3-select-protection-group-type.png)

    此时会打开“选择组成员”屏幕。 

    ![选择组成员](./media/backup-mabs-files-applications-azure-stack/4-opening-screen-choose-servers.png)

3. 在 " **选择组成员** " 屏幕中，选择 **+** 展开子项列表。 选中想要保护的所有项对应的复选框。 选择所有项后，选择 " **下一步**"。

    ![选择要保护的每个项目](./media/backup-mabs-files-applications-azure-stack/5-select-group-members.png)

    Microsoft 建议将共用保护策略的所有数据放入同一个保护组。 有关规划和部署保护组的完整信息，请参阅 System Center DPM 文章[部署保护组](/system-center/dpm/create-dpm-protection-groups)。

4. 在“选择数据保护方法”屏幕中，键入保护组的名称。  选中“我想使用以下介质进行短期保护:”和“我需要在线保护”对应的复选框。   选择“下一步”  。

    ![选择数据保护方法](./media/backup-mabs-files-applications-azure-stack/6-select-data-protection-method.png)

    若要选择“我需要在线保护”，必须先选中“我想使用以下介质进行短期保护: 磁盘”。   Azure 备份服务器不能保护磁带，因此，磁盘是短期保护的唯一选择。

5. 在“指定短期目标”屏幕中，选择要在磁盘中保存恢复点的时间长短，以及何时保存增量备份。  选择“下一步”  。

    > [!IMPORTANT]
    > **不**应在 Azure 备份服务器附加的磁盘上保留操作恢复（备份）数据超过 5 天。
    >

    ![指定短期目标](./media/backup-mabs-files-applications-azure-stack/7-select-short-term-goals.png)

    如果只是在每个计划的恢复点之前运行快速完整备份，而不是选择增量备份的间隔，请选择 " **就在恢复点之前**"。 如果保护的是应用程序工作负荷，Azure 备份服务器会根据同步频率计划创建恢复点（前提是应用程序支持增量备份）。 如果应用程序不支持增量备份，Azure 备份服务器会运行快速完整备份。

    对于“文件恢复点”，请指定何时创建恢复点。  选择 " **修改** " 以设置创建恢复点的时间和日期。

6. 在“检查磁盘分配”  屏幕中，检查为保护组分配的存储池磁盘空间。

    “总数据大小”是要备份的数据大小，“要在 Azure 备份服务器上预配的磁盘空间”是为保护组建议的空间。   Azure 备份服务器会根据设置选择理想的备份卷。 但是，可以在“磁盘分配详细信息”中编辑备份卷选项。 对于工作负荷，请在下拉菜单中选择首选的存储。 编辑操作会更改“可用磁盘存储”窗格中的“存储总量”和“可用存储”值。 预配不足的空间是 Azure 备份服务器建议添加到卷以便将来继续顺利备份的存储量。

7. 在“选择副本创建方法”中，选择要如何处理初始完整数据复制。  如果确定通过网络复制，Azure 建议选择非高峰时间。 如果数据量很大或网络状态欠佳，请考虑使用可移动媒体复制数据。

8. 在“选择一致性检查选项”中，选择要如何自动执行一致性检查。  使一致性检查仅在数据复制变得不一致时才运行，或根据计划运行。 如果不想配置自动一致性检查，可随时通过以下方式运行手动检查：
    * 在 Azure 备份服务器控制台的“保护”区域中，右键单击保护组，并选择“执行一致性检查”。  

9. 如果选择备份到 Azure，请在“指定在线保护数据”页上，确保选择要备份到 Azure 的工作负荷。 

10. 在“指定在线备份计划”中，指定何时增量备份到 Azure。 

    可将备份计划为每日/每周/每月/每年运行，并指定运行备份的时间/日期。 备份一天最多可以进行两次。 每次备份作业运行时，会通过 Azure 备份服务器磁盘上存储的备份数据的副本在 Azure 中创建数据恢复点。

11. 在“指定在线保留策略”中，指定如何在 Azure 中保留通过每日/每周/每月/每年备份创建的恢复点。 

12. 在“选择在线复制”中，指定如何进行数据的初始完整复制。 

13. 在“摘要”中检查设置。  选择 " **创建组**" 时，将发生初始数据复制。 数据复制完成后，在“状态”  页上，保护组状态显示为“正常”  。 初始备份作业根据保护组设置运行。

## <a name="recover-file-data"></a>恢复文件数据

使用 Azure 备份服务器控制台将数据恢复到虚拟机。

1. 在 Azure 备份服务器控制台中，在导航栏上选择 " **恢复** " 并浏览到要恢复的数据。 在结果窗格中选择数据。

2. 在恢复点部分的日历上，以粗体显示的日期表示可用的恢复点。 选择要恢复的日期。

3. 在“可恢复的项”窗格中，选择要恢复的项。 

4. 在 " **操作** " 窗格中，选择 " **恢复** " 以打开 "恢复向导"。

5. 可按如下所述恢复数据：

    * **恢复到原始位置** - 如果已通过 VPN 连接客户端计算机，则此选项不起作用。 请改用备用位置，然后从该位置复制数据。
    * **恢复到备用位置**

6. 指定恢复选项：

    * 对于“现有版本恢复行为”，请选择“创建副本”、“跳过”或“覆盖”。     仅当恢复到原始位置时才能使用“覆盖”。
    * 对于“还原安全性”，请选择“应用目标计算机的设置”或“应用恢复点版本的安全设置”。   
    * 对于 " **网络带宽使用限制**"，请选择 " **修改** " 以启用网络带宽使用限制。
    * **通知** 选择 **"恢复完成后发送电子邮件**"，并指定将接收通知的收件人。 使用逗号分隔电子邮件地址。
    * 做出选择后，选择 "**下一步**"

7. 查看恢复设置，然后选择 " **恢复**"。

    >[!Note]
    >恢复作业正在进行时，会取消针对选定恢复项运行的所有同步作业。

如果使用新式备份存储 (MBS)，则不支持文件服务器最终用户恢复 (EUR)。 文件服务器 EUR 依赖于卷影复制服务 (VSS)，而新式备份存储并不使用该服务。 如果启用了 EUR，请使用以下步骤恢复数据：

1. 导航到受保护的文件，右键单击文件名并选择“属性”。 

2. 在 " **属性** " 菜单上，选择 " **以前的版本** "，然后选择要恢复的版本。

## <a name="view-azure-backup-server-with-a-vault"></a>使用保管库查看 Azure 备份服务器

若要在 Azure 门户中查看 Azure 备份服务器实体，可以按照下列步骤操作：

1. 打开恢复服务保管库。
2. 选择 "备份基础结构"。
3. 查看备份管理服务器。

## <a name="next-steps"></a>后续步骤

有关使用 Azure 备份服务器保护其他工作负荷的信息，请参阅以下文章之一：

* [备份 SharePoint 场](./backup-mabs-sharepoint-azure-stack.md)
* [备份 SQL Server](./backup-mabs-sql-azure-stack.md)
