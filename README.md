# Drupal-solutions
How to get the data from the API by using POST method


<?php

namespace Drupal\rp_region\Controller;

use Drupal;
use Drupal\Core\Controller\ControllerBase;
use Symfony\Component\HttpFoundation\Request;
use Symfony\Component\HttpFoundation\Response;
use Drupal\node\Entity\Node;
use Drupal\file\Entity\File;
use Aws\S3\S3Client;


/**
 * Controller routines for redpepper-api routes.
 */
class ProductImageDeleteController extends ControllerBase
{
    /**
     * Callback for `/api/product-image-url/get` API method.
     *
     * @param \Symfony\Component\HttpFoundation\Request $request
     *   Json query request.
     *
     * @return \Symfony\Component\HttpFoundation\JsonResponse
     *   Json response.
     */
    public function ProductImageS3Delete(Request $request)
    {
        $data = json_decode($request->getContent(), TRUE);
        $config = Drupal::config('s3fs.settings');
        $key_id = $config->get('access_key');
        $key_secret = $config->get('secret_key');
        $acc_region = $config->get('region');
        $bucket = $config->get('bucket');
        // Instantiate the client.
        $s3 = new S3Client([
            'version' => 'latest',
            'region' => $acc_region,
            'credentials' => [
                'key' => $key_id,
                'secret' => $key_secret,
            ],
        ]);
        $response = new Response();

        /* get the product region image url from the  */
        $product_image_url = $data['product_image_url'];
        foreach ($product_image_url as $image_url) {
        $new_image_url = explode(".net/", $image_url);
        $s3_product_image_uri = $new_image_url[1];
         // print_r($s3_product_image_uri);exit;
        $s3->deleteObject([
            'Bucket' => $bucket,
            'Key' => $s3_product_image_uri,

        ]);
    }

        $response->setContent(json_encode("The product region image has been deleted"));
        $response->headers->set("Content-Type", 'application/json');
        return $response;
    }

}
