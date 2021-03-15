## trigger other build job
```
stage ('Run another job') {
    build job: 'Another_job_name', 
    parameters: [
        [$class: 'StringParameterValue', name: 'parameter_name_1', value: 'parameter_value_1'],
        [$class: 'StringParameterValue', name: 'parameter_name_2', value: 'parameter_value_2']
    ]
}
```
