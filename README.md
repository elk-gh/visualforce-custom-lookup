# Custom Lookup for Visualforce
This visualforce component allows you to add lookup field whithout a sobject field reference

<a href="https://githubsfdeploy.herokuapp.com?owner=dieffrei&repo=visualforce-custom-lookup">
  <img alt="Deploy to Salesforce"
       src="https://raw.githubusercontent.com/afawcett/githubsfdeploy/master/src/main/webapp/resources/img/deploy.png">
</a>

## Main features
1. Define on-the-fly what fields you want by lookup
2. Users can create new record by lookup popup
3. Event handler
4. It's open source, change and contribute as you want


## Dynamic visualforce lookup using lookup field

```
<c:Lookup record="{!record}" 
	field="AccountId" 
	searchFields="Name, Rating, BillingCity" 
	formFields="Name, Rating, BillingStreet, BillingState, BillingPostalCode, BillingCountry"
	canCreate="true"
	onchange="refresh"/>
```

## Dynamic visualforce lookup without field
```
<c:AdvancedLookup 
	sObjectName="Order"
	value="{!orderValue}" 
	searchFields="OrderNumber, ContractId, Status, AccountId, EffectiveDate" 
	formFields="OrderNumber, ContractId, Status, AccountId, EffectiveDate"
	canCreate="true"
	fieldLabel="OrderNumber"
	onchange="onChangeOrdertHandler"/>

```

## Visualforce example
```
<apex:page showHeader="true" sidebar="false" standardController="Contact" extensions="ContactCtrl">
	<apex:form>
		<apex:sectionHeader title="Contact Edit" subtitle="New Contact" />

		<apex:actionFunction name="refresh" action="{!refresh}" reRender="valueSection" />

		<script>

			function onChangeCarHandler(name, id) {
				console.debug(name, id);
				refresh();
			}

			function onChangeOrdertHandler(name, id) {
				console.debug(name, id);
				refresh();
			}

			function onChangeContractHandler(name, id) {
				console.debug(name, id);
				refresh();
			}

		</script>

		<apex:pageBlock title="Contact Edit" mode="edit">
			<apex:pageBlockButtons location="top">
				<apex:commandButton value="Save" action="{!save}" />
				<apex:commandButton value="Cancel" action="{!cancel}" />
			</apex:pageBlockButtons>
			<apex:pageBlockSection title="Contact Information" columns="2">
				
				<apex:inputField value="{!Contact.FirstName}"/>
				<apex:inputField value="{!Contact.LastName}"/>

				<apex:pageBlockSectionItem>
					<apex:outputLabel>{!$ObjectType.Contact.fields.AccountId.Label}</apex:outputLabel>
					<c:Lookup record="{!record}" 
						field="AccountId" 
						searchFields="Name, Rating, BillingCity" 
						formFields="Name, Rating, BillingStreet, BillingState, BillingPostalCode, BillingCountry"
						canCreate="true"
						onchange="refresh"/>
				</apex:pageBlockSectionItem>

				<apex:pageBlockSectionItem>
					<apex:outputLabel>Order</apex:outputLabel>
					<c:AdvancedLookup 
						sObjectName="Order"
						value="{!orderValue}" 
						searchFields="OrderNumber, ContractId, Status, AccountId, EffectiveDate" 
						formFields="OrderNumber, ContractId, Status, AccountId, EffectiveDate"
						canCreate="true"
						fieldLabel="OrderNumber"
						onchange="onChangeOrdertHandler"/>
				</apex:pageBlockSectionItem>

				<apex:pageBlockSectionItem>
					<apex:outputLabel>Contract</apex:outputLabel>
					<c:AdvancedLookup 
						sObjectName="Contract"
						value="{!contractValue}" 
						searchFields="ContractNumber, AccountId, Status, StartDate, EndDate, ContractTerm" 
						formFields="AccountId, Status, StartDate, EndDate, ContractTerm"
						canCreate="true"
						fieldLabel="ContractNumber"
						onchange="onChangeContractHandler"/>
				</apex:pageBlockSectionItem>

				<apex:pageBlockSectionItem>
					<apex:outputLabel>Car</apex:outputLabel>
					<c:AdvancedLookup 
						sObjectName="Car__c"
						value="{!carValue}" 
						searchFields="Name, PlateTxt__c" 
						formFields="Name, PlateTxt__c"
						canCreate="true"
						onchange="onChangeCarHandler"/>
				</apex:pageBlockSectionItem>

			</apex:pageBlockSection>

			<apex:pageBlockSection id="valueSection" title="Bind values" columns="2">
				<apex:pageBlockSectionItem>
					<apex:outputLabel>Order field:</apex:outputLabel>
					<apex:outputText value="{!orderValue}" />
				</apex:pageBlockSectionItem>
				<apex:pageBlockSectionItem>
					<apex:outputLabel>Contract field:</apex:outputLabel>
					<apex:outputText value="{!contractValue}" />
				</apex:pageBlockSectionItem>
				<apex:pageBlockSectionItem>
					<apex:outputLabel>Car field:</apex:outputLabel>
					<apex:outputText value="{!carValue}" />
				</apex:pageBlockSectionItem>
				<apex:pageBlockSectionItem>
					<apex:outputLabel>Contact.AccountId field:</apex:outputLabel>
					<apex:outputText value="{!record.AccountId}" />
				</apex:pageBlockSectionItem>
			</apex:pageBlockSection>

		</apex:pageBlock>
	</apex:form>
</apex:page>
```

## Visualforce controller example
```
public with sharing class ContactCtrl 
{

    public LookupValue orderValue {get;set;}
    public LookupValue contractValue {get;set;}
	public LookupValue carValue {get;set;}

	public Contact record;

    public ContactCtrl(ApexPages.StandardController stdController) 
    {
    	stdController.addFields(new List<String>{'AccountId', 'Name'});
        this.record = (Contact)stdController.getRecord();

        //initialize advanced lookup's value
        this.orderValue = new LookupValue();
        this.carValue = new LookupValue();
        this.contractValue = new LookupValue();
    }

    public void refresh()
    {

    }

}
```

# Knowed issues
> No such column 'LastViewedDate' on entity

According to salesforce support, LastViewedDate is only available for those Custom Objects has custom tab,
So to enable recent viewed record, you need to do this workaround
https://help.salesforce.com/apex/HTViewSolution?id=000230533&language=en_US