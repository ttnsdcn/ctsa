# Autocovariance, Autocorrelation and Partial Autocorrelation Functions #

### Autocovariances ###

```

void acvf(double* inp, int N, double* acov, int M);// Default Method

// inp - Input Time Series
// N - Length of the Time Series
// acov - Autocovariance Function
// M - Length of Autocovariance Vector. Lag 0 to Lag M-1

```

```

void acvf_opt(double* inp, int N, int method, double* acov, int M);

// method = 0 ; Default Method
// method = 1 ; FFT Based method. 

// inp - Input Time Series
// N - Length of the Time Series
// acov - Autocovariance Function
// M - Length of Autocovariance Vector. Lag 0 to Lag M-1

```

### Autocovariance To Autocorrelation Conversion ###

```

void acvf2acf(double *acf, int M);

// acf is the autocovariance vector of length M at Input
// acf is the autocorrelation vector of length M at output
```

## Example ##

| **[Latest Version Of Code Here](https://code.google.com/p/ctsa/source/browse/test/acftest.c)** |
|:-----------------------------------------------------------------------------------------------|

```

#include <stdio.h>
#include <stdlib.h>
#include <math.h>
#include "../header/ctsa.h"

int main(void) {
        int i, N, M;
        double *inp, *acf;
        int method;

        /*
        acvf and acvf_opt functions will calculate autocovariance from lag 0 to lag M-1
        */

        FILE *ifp;
        double temp[1200];

        ifp = fopen("seriesC.txt", "r");
        i = 0;
        if (!ifp) {
                printf("Cannot Open File");
                exit(100);
        }
        while (!feof(ifp)) {
                fscanf(ifp, "%lf \n", &temp[i]);
                i++;
        }
        N = i;

        inp = (double*)malloc(sizeof(double)* N);
        //wmean = mean(temp, N);

        for (i = 0; i < N; ++i) {
                inp[i] = temp[i];
                //printf("%g \n",inp[i]);
        }

        M = 10;
        acf = (double*)malloc(sizeof(double)* M);

        // Default Method

        printf("\n Default Method : acvf \n");

        acvf(inp, N, acf, M);

        for (i = 0; i < M; ++i) {
                printf("%g ", acf[i]);
        }
        printf("\n");

        // acvf_opt : Method 0 General Method
        method = 0;
        printf("\n acvf_opt Method 0 General Method \n");
        acvf_opt(inp, N, method, acf, M);

        for (i = 0; i < M; ++i) {
                printf("%g ", acf[i]);
        }

        printf("\n");
        // acvf_opt : Method 1 FFT
        method = 1;
        printf("\n acvf_opt Method 1 FFT \n");
        acvf_opt(inp, N, method, acf, M);

        for (i = 0; i < M; ++i) {
                printf("%g ", acf[i]);
        }
        printf("\n \n");

        // Calculate Autocorrelation from already caluclated Covariance function
        printf("Autocorrelation \n");
        acvf2acf(acf, M);

        for (i = 0; i < M; ++i) {
                printf("%g ", acf[i]);
        }
        printf("\n");

        free(inp);
        free(acf);
        return 0;

}


```


![https://lh6.googleusercontent.com/-McmH9dMGW34/VFODCoB4pnI/AAAAAAAAAbE/ygaSIP9SKPo/s1145/acftest.png](https://lh6.googleusercontent.com/-McmH9dMGW34/VFODCoB4pnI/AAAAAAAAAbE/ygaSIP9SKPo/s1145/acftest.png)

### Partial Autocorrelations ###

```

void pacf(double* inp, int N, double* par, int M); //Default Method (Yule Walker)

// inp - Input Time Series
// N - Length of the Time Series
// par - Partial Autocorrelation Function
// M - Length of Partial Autocorrelation Vector. Lag 0 to Lag M-1

```

```

void pacf_opt(double* inp, int N, int method, double* par, int M);

// method = 0; Yule-Walker
// method = 1; Burg
// method = 2; Conditional MLE (Box-Jenkins)

// inp - Input Time Series
// N - Length of the Time Series
// par - Partial Autocorrelation Function
// M - Length of Partial Autocorrelation Vector. Lag 0 to Lag M-1

```

## Example ##

| **[Latest Version Of Code Here](https://code.google.com/p/ctsa/source/browse/test/pacftest.c)** |
|:------------------------------------------------------------------------------------------------|

```

#include <stdio.h>
#include <stdlib.h>
#include <math.h>
#include "../header/ctsa.h"

int main(void) {
        int i, N, M;
        double *inp, *par;
        int method;

        FILE *ifp;
        double temp[1200];

        ifp = fopen("seriesC.txt", "r");
        i = 0;
        if (!ifp) {
                printf("Cannot Open File");
                exit(100);
        }
        while (!feof(ifp)) {
                fscanf(ifp, "%lf \n", &temp[i]);
                i++;
        }
        N = i;

        inp = (double*)malloc(sizeof(double)* N);
        //wmean = mean(temp, N);

        for (i = 0; i < N; ++i) {
                inp[i] = temp[i];
                //printf("%g \n",inp[i]);
        }

        M = 10;
        par = (double*)malloc(sizeof(double)* M);

        // Default Method is Yule-Walker

        printf("\n Default Method : pacf \n");

        pacf(inp, N, par, M);

        for (i = 0; i < M; ++i) {
                printf("%g ", par[i]);
        }
        printf("\n");

        // pacf_opt : Method 0 Yule Walker
        method = 0;
        printf("\n pacf_opt Method 0 Yule Walker \n");
        pacf_opt(inp, N, method, par, M);

        for (i = 0; i < M; ++i) {
                printf("%g ", par[i]);
        }

        printf("\n");
        // pacf_opt : Method 1 Burg
        method = 1;
        printf("\n pacf_opt Method 1 Burg \n");
        pacf_opt(inp, N, method, par, M);

        for (i = 0; i < M; ++i) {
                printf("%g ", par[i]);
        }
        printf("\n");

        // pacf_opt : Method 2 MLE (Box-Jenkins)
        method = 2;
        printf("\n pacf_opt Method 2 MLE (Box-Jenkins) \n");
        pacf_opt(inp, N, method, par, M);

        for (i = 0; i < M; ++i) {
                printf("%g ", par[i]);
        }
        printf("\n");

        free(inp);
        free(par);
        return 0;

}


```

![https://lh6.googleusercontent.com/-D1pYH0RnjrI/VFODGfEpnnI/AAAAAAAAAbM/qSgSZiyZUF4/s1363/pacftest.png](https://lh6.googleusercontent.com/-D1pYH0RnjrI/VFODGfEpnnI/AAAAAAAAAbM/qSgSZiyZUF4/s1363/pacftest.png)