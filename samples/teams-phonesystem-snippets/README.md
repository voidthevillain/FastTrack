# Microsoft FastTrack Open Source - Teams Phone System Snippets

Here are a few common Teams Phone System configuration PowerShell snippets for number configuration and other related tasks.

## Usage

These snippets are not provided as PowerShell scripts as they are only a few lines each, and would often be run interactively or as a one-off.

Note we do assume the MicrosoftTeams PowerShell module has been installed and signed in. For assistance, please see the following docs page:

- [Microsoft Teams PowerShell](https://docs.microsoft.com/en-us/MicrosoftTeams/teams-powershell-install)

After installing the Microsoft Teams module, here's an example of connecting to remote Teams/Skype for Business Online PowerShell:

```PowerShell
Import-Module MicrosoftTeams
Connect-MicrosoftTeams
```

**Note:** Ensure you are running the 4.6.0 (July 2022) or later version of the MicrosoftTeams module. You can verify installed versions with `Get-Module MicrosoftTeams -ListAvailable`, and if needed install the latest update from an elevated PowerShell session with `Update-Module MicrosoftTeams`

If you need a quick start creating an input csv for the below examples, download your full list of Skype/Teams users and save off the desired user rows to be the input CSV file from this export:

```PowerShell
Get-CsOnlineUser -ResultSize Unlimited | Export-Csv "C:\path\to\allusers.csv"
```

## Assign phone numbers to users in bulk from CSV

Numbers can be assigned individually from the Teams admin center, but can also be assigned via PowerShell in bulk by reading an appropriate CSV input file with the necessary minimum information. When assigning Teams Calling Plan numbers, the LocationID is required to be specified - this is the emergency address that is statically assigned to the phone number for emergency calling purposes. The LocationId value can be copied from the Teams admin center for the location entry, or from PowerShell with `Get-CsOnlineLisLocation`.

**_Input CSV needs these columns:_**

- UserPrincipalName
- PhoneNumber
- PhoneNumberType*
- LocationId**

\* _Possible values for PhoneNumberType:_

- CallingPlan
- OperatorConnect
- OCMobile
- DirectRouting

\*\* _LocationId only required for CallingPlan phone number type assignment_

```PowerShell
$assignphoneusers = Import-Csv "C:\path\to\assignphoneusers.csv"

foreach ($user in $assignphoneusers) {
    if ($user.PhoneNumberType -eq "CallingPlan") {
        Set-CsPhoneNumberAssignment -Identity $user.UserPrincipalName -PhoneNumber $user.PhoneNumber -PhoneNumberType $user.PhoneNumberType -LocationId $user.LocationId
    } else {
        Set-CsPhoneNumberAssignment -Identity $user.UserPrincipalName -PhoneNumber $user.PhoneNumber -PhoneNumberType $user.PhoneNumberType
    }
}
```

## Author

|Author|Last Updated Date
|----|--------------------------
|David Whitney, Microsoft|November 8, 2022|

## Issues

Please report any issues you find to the [issues list](https://github.com/microsoft/FastTrack/issues).

## Support Statement

The scripts, samples, and tools made available through the FastTrack Open Source initiative are provided as-is. These resources are developed in partnership with the community and do not represent official Microsoft software. As such, support is not available through premier or other Microsoft support channels. If you find an issue or have questions please reach out through the issues list and we'll do our best to assist, however there is no associated SLA.

## Code of Conduct

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/).
For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or
contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.

## Legal Notices

Microsoft and any contributors grant you a license to the Microsoft documentation and other content in this repository under the [MIT License](https://opensource.org/licenses/MIT), see the [LICENSE](LICENSE) file, and grant you a license to any code in the repository under the [MIT License](https://opensource.org/licenses/MIT), see the [LICENSE-CODE](LICENSE-CODE) file.

Microsoft, Windows, Microsoft Azure and/or other Microsoft products and services referenced in the documentation may be either trademarks or registered trademarks of Microsoft in the United States and/or other countries. The licenses for this project do not grant you rights to use any Microsoft names, logos, or trademarks. Microsoft's general trademark guidelines can be found at http://go.microsoft.com/fwlink/?LinkID=254653.

Privacy information can be found at https://privacy.microsoft.com/en-us/

Microsoft and any contributors reserve all others rights, whether under their respective copyrights, patents,
or trademarks, whether by implication, estoppel or otherwise.
