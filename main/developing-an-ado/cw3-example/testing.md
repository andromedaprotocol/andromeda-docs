# Testing

Testing an ADO is similar to testing any standard CosmWasm contract. We will take a look at one test and note some minor differences:

{% tabs %}
{% tab title="Multisig ADO" %}
```rust
fn test_instantiate_works() {
    let mut deps = mock_dependencies_custom(&[]);
    let info = mock_info(OWNER, &[]);

    let max_voting_period = Duration::Time(1234567);

    // No voters fails
    let instantiate_msg = InstantiateMsg {
        kernel_address: MOCK_KERNEL_CONTRACT.to_string(),
        owner: None,
        voters: vec![],
        threshold: Threshold::ThresholdQuorum {
            threshold: Decimal::zero(),
            quorum: Decimal::percent(1),
        },
        max_voting_period,
    };
    
/// the rest of the test is the same in both versions
```
{% endtab %}

{% tab title="Multisig CW3" %}
```rust
fn test_instantiate_works() {
        let mut deps = mock_dependencies();
        let info = mock_info(OWNER, &[]);

        let max_voting_period = Duration::Time(1234567);

        // No voters fails
        let instantiate_msg = InstantiateMsg {
            voters: vec![],
            threshold: Threshold::ThresholdQuorum {
                threshold: Decimal::zero(),
                quorum: Decimal::percent(1),
            },
            max_voting_period,
        };
        
      /// the rest of the test is the same in both versions
```
{% endtab %}
{% endtabs %}

* Here we use the `mock_dependencies_custom` instead of `mock_dependencies`, which automatically assigns a kernel address (`MOCK_KERNEL_CONTRACT`).
* The ADO `InstantiateMsg` includes `kernel_address` and `owner` fields.

The rest of the test logic is the same.

### Further Testing

As mentioned [before](../getting-started/#testing), we also provide custom mock structs for testing. You can check any of our published ADOs testing from our [core repo](https://github.com/andromedaprotocol/andromeda-core) to see how these structs can be used to conduct testing.&#x20;
