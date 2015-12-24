# AR Class And Functions #

**Important** - As of 10/30/2014 the class isn't fully implemented. It isn't flexible enough and it won't give users choice to set AR parameters. However, the underlying AR algorithms and codes have been tested and they should work properly. Additionally, ar\_predict uses a modified version of Algorithm AS182 and it needs some more work.

```

ar_object ar_init(int method, int N);

// N - Length of the Time Series
// method = 0; Yule Walker
// method = 1; Burg
// method = 2; MLE

```

### Execution ###

```

void ar_exec(ar_object obj, double *inp);
// obj - AR object
// inp - Input Time series of length N

```

### AR Object Parameters ###

```

        int N;// length of time series
        int method; // See Above
        int optmethod;// Valid only for MLE estimation. See Below
        int p;// size of phi 
        int order; // order = p
        int ordermax; // Maximum order to be fit
        double *phi; // Autoregression Coefficients
        double *res; // Residuals. Implemented Only for MLE method
        double mean; // mean
        double var; // Variance
        double aic; // AIC criteria. Implemented Only for MLE method
        int retval; // Return Value. Implemented Only for MLE method. See ARIMA class for values.


```

### Functions associated with ARIMA method ###

```

void ar_summary(ar_object obj); // Prints AR results and summary.

void ar_predict(ar_object obj, double *inp, int L, double *xpred, double *amse); // L-step Prediction for time series inp (length N). Returns length L output xpred and length L mean square error amse vectors

```

### Deallocate Object Obj when the computation is finished ###
```
 ar_free(obj);
```

## Example ##

| **[Latest Version Of Code Here](https://code.google.com/p/ctsa/source/browse/test/artest.c)** |
|:----------------------------------------------------------------------------------------------|

```

#include <stdio.h>
#include <stdlib.h>
#include <math.h>
#include "../header/ctsa.h"

int main(void) {
        int i, N, L,method;
        double *inp;
        int p;
        double *phi;
        double *xpred, *amse;

        ar_object obj;
        p = 0;


        L = 5;

        phi = (double*)malloc(sizeof(double)* p);

        xpred = (double*)malloc(sizeof(double)* L);
        amse = (double*)malloc(sizeof(double)* L);

        FILE *ifp;
        double temp[2000];

        ifp = fopen("seriesA.txt", "r");
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

        method = 0; // method 0 - Yule Walker, Method 1 - Burg, Method 2, MLE (Box-Jenkins)
        obj = ar_init(method, N);
        ar_exec(obj, inp);
        ar_summary(obj);
        // Predict the next 5 values using the obtained ARIMA model
        ar_predict(obj, inp, L, xpred, amse);
        printf("\n");
        printf("Predicted Values : ");
        for (i = 0; i < L; ++i) {
                printf("%g ", xpred[i]);
        }
        printf("\n");
        printf("Standard Errors  : ");
        for (i = 0; i < L; ++i) {
                printf("%g ", sqrt(amse[i]));
        }
        printf("\n");

        ar_free(obj);
        //ar_estimate(inp, N, 2);
        free(inp);
        free(phi);
        free(xpred);
        free(amse);
        return 0;

}

```

![https://lh6.googleusercontent.com/-f9R1PHuQh_8/VFIE3qJpssI/AAAAAAAAAa0/BgFkGLvzorY/s1315/artest.png](https://lh6.googleusercontent.com/-f9R1PHuQh_8/VFIE3qJpssI/AAAAAAAAAa0/BgFkGLvzorY/s1315/artest.png)