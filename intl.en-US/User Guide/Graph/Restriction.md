# Restriction {#concept_dhm_wnm_vdb .concept}

The restrictions of MaxCompute Graph are as follows:

-   Each job can reference up to 256 resources. A table or an archive is considered as one unit \(that is, one resource）.
-   The total number of bytes of resources referenced by one job cannot exceed 512 MB. Each job can reference up to 512 MB of bytes of resources.
-   The number of inputs of one job cannot exceed 1,024. \(The number of input tables cannot exceed 64\). The number of outputs of one job cannot exceed 256.
-   Label conventions are as follows: A label can be up to 256 characters in length and can contain letters, numbers, underscores ‘\_’, pound signs ‘\#’, periods ‘.’, and hyphens ‘-‘. Labels specified for multiple outputs cannot be null or empty strings.
-   Each job can have up to 64 custom counters. The group name and counter name can be up to 100 characters in length. The names  cannot contain pound signs ‘\#’.
-   The number of Workers of one job is calculated by the framework. The maximum number is 1,000. If this threshold value is exceeded, an exception is thrown.
-   One Worker occupies 200 resources of the CPU by default. The range is \[50, 800\].
-   One Worker occupies 4096 MB of the memory by default. The range is \[256 MB, 12 GB\].
-   The number of times for repeatedly reading a resource by one Worker cannot exceed 64.
-   The split size is 64 MB by default, which can be set. The range is 0 < split\_size <= \(9223372036854775807 \>\>  20\).
-   In the MaxCompute Graph program, GraphLoader/Vertex/Aggregator running in a cluster is restricted by the Java sandbox. \(The main program of Graph jobs is not restricted.\)  For more information about the restrictions, see  [Java sandbox](intl.en-US/User Guide/Java Sandbox.md).

