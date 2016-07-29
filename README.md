# openshift-secure-routes
Example of secure route in openshift

## Content
* [Testing with Vagrant](#testing-with-vagrant)
* [Testing with Atomic](#testing-with-atomic)

## Testing with Vagrant

1. Set up the OpenShift CDK, described by RedHat: [RedHat OpenShift Site](http://developers.redhat.com/products/cdk/get-started/)

2. Clone this repository:

        git clone https://github.com/fsimorbrian/openshift-secure-routes
        
3. Change to cloned repo directory:

        cd openshift-secure-routes
        
4. Run vagrant up, wait for services to completely start up.

        vagrant up

5. Connect to OpenShift on the just built CDK OpenShift VM.

        https://10.1.2.2:8443/console/
        login with admin/admin

## Testing with Atomic

1. Build a VM with Atomic as base OS, Install OpenShift.

2. Run

5. Connect to OpenShift on the just built Atomic OpenShift VM.

        https://<hostname-or-ip>:8443/console/
        login with admin/admin

## Testing Secure Route
   
1. Connect to the OpenShift host you want to test (Atomic or Vagrant IP)

2. Click **secure-sample** project in the OpenShift Console.

3. Click **Create Route** on upper right of **mynginx** service.

4. Create Route Info:

        Name: mynginx
        Hostname: nginx.morbrian.com
        Path: /
        Target Port: 443 --> 443 (TCP)
        Expanded options for secured routes:
        TLS Termination: Passthrough
        
5. Edit your hosts file locally, the following entry works with CDK/Vagrant,
but when testing Atomic, change out the IP for what your Atomic VM is configured with:

        10.1.2.2        nginx.morbrian.com
        
6. Verify nginx is accessible from browser

        https://nginx.morbrian.com/
        Expected result: 404 Not Found, subtitled nginx/1.9.2
