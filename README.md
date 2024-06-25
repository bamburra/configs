#cria uma inferencia que sera utilizada dentro do pipeline
PUT _inference/text_embedding/bamburra_inference
{
    "service": "cohere",
    "service_settings": {
        "api_key": "7EWBxohRsortyQ3pP3hpRXG6Hiu72hRKl3S2proP", 
        "model_id": "embed-multilingual-v3.0", 
        "embedding_type": "byte"
    }
}
#cria pipeline utilizando a inferencia, que sera utilizado
# como pipeline padrao ao indexar algo dentro do index
PUT _ingest/pipeline/bamburra_embeddings
{
  "processors": [
    {
      "inference": {
        "model_id": "bamburra_inference", 
        "input_output": { 
          "input_field": "content",
          "output_field": "content_embedding"
        }
      }
    }
  ]
}


#Cria index que utiliza como padrao o pipeline criado
# para indexar documentos já processando seus embeddings
PUT /bamburra_lote_document_embedding
{
  "settings": {
    "index": {
      "default_pipeline": "bamburra_embeddings"
    }
  },
  "mappings": {
    "properties": {
      "content_embedding": {
        "type": "dense_vector",
        "dims": 1024,
        "element_type": "byte"
      },
      "content": {
        "type": "text"
      }
    }
  }
}
